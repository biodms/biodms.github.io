---
title: 'From Guardrails to Compilers: Making Clinical AI Agents Safe by Construction'
slug: from-guardrails-to-compilers
date: 2026-07-25
summary: |
  We reframe clinical AI safety as a runtime problem, compiling every agent action into a constrained, provenance-tracked query plan that enforces contraindications, evidence, and access rules by construction.
thumbnail: images/thumbnail.png
authors:
  page: people
  names:
    - kaiping-zheng
    - horng-ruey-chua
    - si-wei-kheok
    - kee-yuan-ngiam
    - marcus-tan-chun-jin
    - robert-john-walsh
links:
  - icon: fa-regular fa-file-lines
    href: https://openreview.net/forum?id=KtgISugg4l
    large: true
    label: Paper
---

A molecular tumor board at a busy cancer center works through dozens of complex cases in a single sitting, with only minutes for each. Behind every patient sits a mountain of data: three electronic health record systems, two genomic-sequencing vendors, pathology, prior imaging, past treatments, and a research literature that changes every week. Down the hall, an ICU physician faces the opposite pressure: the same flood of data but a decision cycle measured in minutes, where missing a single contraindication can cost a life. Both settings share a hard truth: too much data and too many rules to weigh by hand, and too little time to reason from scratch. Months later comes the same uncomfortable question from an auditor or a lawyer: which evidence supported this decision, and was the standard of care met?

It is no surprise that hospitals are experimenting with AI agents built on large language models (LLMs) to read records, pull evidence, and draft recommendations. The promise is real, but so is the problem.

## The sobering evidence

Recent clinical studies paint a worrying picture. Agents invent drug dosages and skip standard-of-care steps. They induce automation bias: in one randomized trial, physicians who had received formal AI-literacy training still lost 14 points of diagnostic accuracy when shown a plausible but wrong AI suggestion. These systems are also easy to attack; one study coaxed harmful medical advice out of a leading model through simple prompt injection more than 90% of the time. When a decision is later contested, the agent often cannot produce a defensible trail of why it recommended what it did.

The instinctive fix from the AI community is to add guardrails: post-hoc filters, "please cite your sources" prompts, and self-checks bolted onto the outside of a black-box text generator. Guardrails help, but they treat the agent as an opaque author whose output must be policed after the fact. Anyone who has watched an alert-fatigued clinician click through a stale pop-up knows how leaky that approach is.

## A different lens: a runtime problem, not a model problem

Look at what an agent actually does under the hood: it runs a query over messy, governed, uncertain data. It fetches this record, joins it with that guideline, calls this tool, retrieves that paper, and combines the results. Seen this way, an LLM is just one operator among many in a larger plan, and the database community has spent decades learning how to make such plans correct, secure, and auditable by construction.

So we argue that the failures above are runtime failures, not model failures. The substrate on which today's medical agents run simply lacks the machinery, long standard in data systems, to enforce rules, track where every fact came from, and respect who is allowed to see what.

Our proposal is to stop treating the natural-language prompt as the final word and start treating it as the front end of a compiler. Every agent action, whether a tool call, a retrieval, a semantic operation, or a final recommendation, is compiled into a constrained, provenance-tracked query plan. Safety is enforced inside the plan, not filtered off the text afterward.

## What that looks like: one concrete example

Take a real constraint. Cetuximab, a common colorectal-cancer drug, is contraindicated when the tumor carries an activating KRAS mutation. A guardrail-wrapped LLM, asked "what are this patient's options?", may suggest it anyway.

In our runtime, the same request compiles into a plan. An access-controlled query fetches the patient's genomic result and medication list from the right institutions. A semantic operator drafts candidate regimens. A declarative integrity constraint, essentially a forbidden combination of (mutation, drug class, recommendation), then fires the moment a KRAS-plus-anti-EGFR pairing appears and rewrites the plan to warn and log rather than recommend. Every step is stamped into a tamper-evident record. The unsafe recommendation never reaches the clinician, and the reasoning can be reconstructed in full later.

## Borrowing tools the database community already built

The encouraging part is that we are not inventing these mechanisms from scratch. Each clinical need maps onto a primitive that already exists and has been validated outside medicine:

- **Semantic integrity constraints** to encode contraindications and drug-interaction rules.
- **Ontologies** (SNOMED, LOINC, RxNorm) so that clinical claims are grounded rather than free-floating text an attacker can spoof.
- **Provenance**, a compositional algebra that tracks why each fact in a result exists, for evidence a tumor-board chair can actually sign off on.
- **Secure federated query processing** so an agent can compare "patients like this" across hospitals without violating privacy law.
- **Incremental view maintenance** to keep up as guidelines are revised, several times a year.

Composing these into a single specification turns the messy clinical context into a first-class compilation target. The result is what we call contextual intelligence, and, crucially, it is a property of the runtime rather than of the language model running inside it.

## Honest about the hard parts

This is a vision, not a finished system, and we are candid about the open challenges. Guidelines are not code: they contain ambiguity, exceptions, and genuine expert judgment, so we compile only the computable subset and treat the rest as soft norms that can be overridden. Clinician overrides must remain first-class, so we draw a sharp line between a justified override, a recorded and attributable deviation from a soft norm, and an agent failure, a violation of a hard rule that the runtime should have caught but did not. None of this is free: constraint checking, provenance, and auditing all add overhead, so the runtime has to balance expressiveness against what can actually be enforced inside a clinical decision cycle.

## Why it matters

The path to safe clinical AI at the bedside is not simply a bigger model. It is a substrate that compiles whatever model you use into plans that a hospital, a regulator, and an auditor can inspect, backed by a signed evidence dossier instead of a model card. That reframes clinical-agent safety as something the data-management community is well equipped to solve, and it is an invitation to build it together.
