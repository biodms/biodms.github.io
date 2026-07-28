---
title: 'User Defined Functions as a First-Class Citizen for Text-to-SQL on Medical Databases'
slug: user-defined-functions-as-a-first-class-citizen-for-text-to-sql-on-medical-databases
date: 2026-07-28
summary: |
  We introduce MedSQLX, which turns a plain medical question into SQL by synthesizing the missing user-defined functions from trusted clinical knowledge and verifying them against the database before answering.
thumbnail: images/thumbnail.png
authors:
  page: people
  names:
    - catlynh-nguyen
    - irbaz-bin-riaz
    - chitta-baral
    - jia-zou
links:
  - icon: fa-regular fa-file-lines
    href: https://openreview.net/forum?id=fcR4RiawOZ
    large: true
    label: Paper
references:
  data: references.yaml
---

## The Gap Between Querying a Database and Querying Medical Knowledge

Consider a simple question: "What is the longest consecutive streak of elevated creatinine readings for this patient?" A clinician can easily understand what this question asks, but expressing it in SQL is far from straightforward. The query must sort laboratory results chronologically, determine whether each reading is elevated using gender-specific clinical thresholds, and identify the longest consecutive sequence satisfying the condition. Similarly, assessing whether a patient's potassium levels are "unusually volatile" requires computing statistics over longitudinal measurements, while calculating diabetes risk combines multiple laboratory values and demographic factors according to clinical scoring guidelines. These kinds of computations go well beyond what can be naturally expressed using SQL's WHERE, CASE, or aggregation clauses, and are more naturally encapsulated as pieces of custom logic called user-defined functions (UDFs), invoked from within a query. The limitation of existing natural-language-to-SQL (NL-to-SQL) systems is that they assume the UDFs a query needs already exist. They are effective at writing SQL that calls a function such as `classify_creatinine(value, gender)`. They have no mechanism for the case where that function does not yet exist and must be authored from scratch, grounded in accurate clinical knowledge, and validated before use.

This is the gap that our system, MedSQLX, is designed to close: given only a natural-language medical question, it generates both the missing function and the SQL query that invokes it.

## An Example

Database schemas:

```
patients(
    patient_id,
    gender,
    birth_date,
    ...
)

observations(
    patient_id,
    code,
    value,
    unit,
    observation_time,
    ...
)
```

Generated UDF:

```python
def longest_elevated_streak(readings, gender):
    threshold = 1.3 if gender == "male" else 1.1

    longest = 0
    current = 0

    for val, timestamp in sorted(readings, key=lambda x: x[1]):
        if float(val) > threshold:
            current += 1
            longest = max(longest, current)
        else:
            current = 0

    return longest
```

Generated SQL:

```sql
WITH creatinine_history AS (
    SELECT
        o.patient_id,
        list(
            struct_pack(
                value := CAST(o.value AS DOUBLE),
                ts := o.observation_time
            )
            ORDER BY o.observation_time
        ) AS readings
    FROM observations o
    WHERE o.code = 'creatinine'
    GROUP BY o.patient_id
)

SELECT
    p.patient_id,
    longest_elevated_streak(
        h.readings,
        p.gender
    ) AS longest_streak
FROM patients p
JOIN creatinine_history h
ON p.patient_id = h.patient_id
ORDER BY longest_streak DESC;
```

## Why the Problem Is Harder Than It Appears

A question such as "What is the longest consecutive streak of elevated creatinine readings for this patient?" appears straightforward, but it contains several unstated decisions. What constitutes "elevated"? Is the threshold the same for men and women, or people from different age groups? Should the function return a count, a label, or something else? What input format does it require? A clinician leverages their domain knowledge to resolve these questions, which are missing in existing NL-to-SQL frameworks. In addition, an automated system must either be told the answers or determine them independently.

MedSQLX addresses this through a two-stage LLM-assisted agentic framework. The first, a semantic-analysis stage, identifies which parts of a question are underspecified and infers the most plausible interpretation for each, producing an explicit record of its assumptions before any code is written. This process is also termed Chain-of-Clarifications. The second, a code-generation stage, takes those assumptions and iteratively writes, registers, and tests the corresponding function against a live database. This stage has access to three tools: a medical knowledge retriever that supplies validated clinical domain context, a tool that interacts with the database, and a tool that evaluates the generated function for correctness and clinical soundness. In this process, the following steps repeat: (1) a planner agent decides which tool to use, (2) an executor agent runs the tool, and (3) a verifier agent analyzes the tool execution results, until the verifier decides sufficient information has been obtained to resolve the problem. The generator agent then returns the final SQL code with UDFs to the client. During this process, when an issue is detected, the system revises its output rather than returning code that fails silently.

## Evaluation

We evaluated MedSQLX on ten clinical query scenarios built on MedAgentBench ([Jiang et al., 2025](#jiang2025)), a large, real-world hospital dataset formatted in the manner that modern electronic health record systems expose data. These scenarios ranged from direct classification tasks (does this glucose reading fall in the diabetic range?) to open-ended questions in which even the form of the answer is unspecified (are this patient's potassium levels unusually volatile?).

MedSQLX produced correct results on all 57 test cases across these scenarios. As a point of comparison, we evaluated the same questions using DIN-SQL ([Pourreza and Rafiei, 2023](#pourreza2023)), a state-of-the-art NL-to-SQL system, which succeeded on fewer than half of the cases. The pattern behind this gap is informative: DIN-SQL is not designed to recognize when a required function is missing, to consult a source of medical knowledge before assuming a threshold, or to produce Python logic where a plain SQL query is insufficient. These are the gaps that MedSQLX's tool-based, self-verifying design is intended to address.

## Observations From the Evaluation

Several of the baseline's failures stemmed not from a lack of medical knowledge, but from a lack of structured process. In one case, the system correctly identified a clinical scoring error but never applied the corresponding fix. In another, it relied on a hardcoded date rather than the current date, a defect that would silently produce incorrect results on any subsequent day. These are the kinds of errors that structured tool use and verification loops are well suited to catch, and they account for much of the observed gap between the two systems.

We also note the limitations of this initial study. Fifty-seven test cases across ten scenarios constitute a meaningful proof of concept rather than a comprehensive benchmark. Clinical language in practice is considerably more varied than what was tested here. Our subsequent work is focused on constructing a larger, held-out evaluation set grounded in clinician-sourced questions, in order to assess how well the approach generalizes beyond the scenarios used during development. We will extend this benchmark with clinicians from Mayo Clinic-Arizona.

## Broader Implications

The underlying problem extends well beyond potassium and creatinine. Any domain governed by specialized, evolving rules, such as finance, law, or engineering, encounters the same limitation: a database cannot answer a question phrased in terms that rely on domain knowledge beyond the underlying database schemas and computational logic beyond the SQL language. Systems capable of recognizing a missing piece of domain logic, retrieving the knowledge required to construct UDFs, integrating them correctly into SQL queries, and verifying their own output before returning an answer represent a step toward AI tools that clinicians, analysts, and researchers can rely on for the domain-specific, rule-laden questions they routinely ask.

The medical domain is particularly compelling because clinical decision making is driven by continuously evolving medical knowledge rather than static database fields. Clinical practice guidelines are updated as new evidence emerges, laboratory reference ranges vary across patient populations, risk scores combine dozens of clinical variables, and diagnoses often depend on longitudinal patterns rather than individual observations. Encoding all of these rules manually as database functions is both labor-intensive and difficult to maintain, so many clinically meaningful questions remain inaccessible to conventional Text-to-SQL systems, even when all of the required patient data already reside in the database. Systems such as MedSQLX offer a different paradigm: instead of assuming that every piece of clinical reasoning has already been implemented by database developers, they synthesize missing computational logic on demand from trusted medical knowledge and integrate it into executable SQL queries. Looking forward, we envision extending this capability beyond structured laboratory data to multi-modal clinical databases that combine electronic health records with medical images, clinical notes, genomic data, and physiological signals, helping bridge the gap between rapidly evolving medical knowledge and the databases that store patient information.