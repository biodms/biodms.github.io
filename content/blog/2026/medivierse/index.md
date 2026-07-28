---
title: 'Mediverse: Multimodal Clinical Exploration and Search on a Single Graph'
slug: mediverse-multimodal-clinical-exploration-and-search-on-a-single-graph
date: 2026-07-24
summary: |
  We tackle semantic interoperability in healthcare by unifying multimodal clinical data into a single knowledge graph, enabling harmonisation without lossy transformations and uncertainty-aware exploration.
thumbnail: images/thumbnail.png
authors:
  page: people
  names:
    - andra-ionescu
    - paris-carbone
    - sebastiaan-meijer
    - jayanth-raghothama
links:
  - icon: fa-regular fa-file-lines
    href: https://openreview.net/forum?id=1cDpN0B4G2
    large: true
    label: Paper
references:
  data: references.yaml
---

Interoperability in healthcare has been developed from the bottom-up, layer by layer. It started with the development of exchange mechanisms (e.g., HL7 v2), which enabled hospitals to share structured clinical events. Next, terminology systems such as SNOMED-CT, LOINC, and much older ICD were developed, providing standardised vocabularies to encode clinical concepts. However, even with shared transport and shared vocabularies, the structure of clinical information remained inconsistent. Information models, such as openEHR, responded to this gap, promising to solve interoperability by separating the technical layer (i.e., how data are stored) from the clinical layer (i.e., what the data tell us).

The model-first philosophy and the strict standards required significant upfront resources for modelling and governance, which only a few health systems could afford. Because each layer was standardised independently, no unified information model was agreed upon. Each implementation imposed its own structure at the intersection of these standards, and we could see that hospitals adopting the same standards still produced semantically incompatible datasets. This fragmentation emerged from the architecture of the bottom-up approach, a consequence that no single standard can undo.

Despite these flaws, the bottom-up approach contributed to solutions and advancements that solved technical and syntactic interoperability, which are two of the four levels of interoperability in healthcare: technical, syntactic, semantic, and organisational. We are currently addressing the challenges of semantic interoperability, with the goal of enabling and solving organisational interoperability.

As such, we support a top-down approach, where rather than standardising exchange and hoping semantic agreement follows, the approach begins by defining a shared information model and derives storage, terminology, and exchange from it.

## Multimodal Integration

With this project, we aim to get a step closer to solving semantic interoperability, which is defined as "the ability of two or more systems or components to exchange information and to use the information that has been exchanged" ([Mello et al., 2022](#mello2022)). As such, we work on the core component of semantic interoperability: semantic harmonisation, which is defined as "the process of combining multiple sources and representations of data into a form where items of data share meaning" ([Cunningham et al., 2016](#cunningham2016)).  Unlike conventional approaches, we investigate semantic harmonisation *without* transforming the data into a common format, since doing so is prone to information loss and requires a predefined target schema. Instead, to enable meaningful integration across diverse knowledge domains, representation formats, and data models, Mediverse leverages knowledge graphs to create flexible, standard-agnostic interoperability approaches.

We achieve this by developing a single graph representation of (medical) ontologies. These ontologies are open-source, many are available as graphs, and nearly all of them have bindings (i.e., links between concepts across ontologies) with each other. These representations and bindings are imported into a graph database to provide a single knowledge base of medical/clinical concepts. As illustrated in Figure 1, we want to query data using a single query language (e.g., Cypher) and the expressiveness of graph databases.

{{< figure src="images/ontological_harmonisation.svg" alt="Figure 1: Harmonisation using a single graph of multi-modal data and a sample Cypher query" width="400px" >}}
**Figure 1.** Harmonisation using a single graph of multi-modal data and a sample Cypher query.
{{< /figure >}}

Our first use case connects Electronic Health Records (EHR) data with ontologies. We propose a semi-automatic approach for concept normalisation, a sub-problem of semantic alignment, which links individual data values from EHR to concepts in a standardised vocabulary.  EHR data comes in multiple formats, the most common being free text (i.e., medical notes) and tabular values (i.e., tables with patient records, admissions, prescriptions, etc). Semantic alignment in EHR data requires integrating contextual, structural, and domain-specific signals to ensure correct interpretation across heterogeneous data sources. Thus, the type of EHR data determines the approaches we need for integration. Free text carries richer context, thus we apply biomedical entity linking solutions. Tabular values carry column/row context, thus we apply standard schema matching techniques alongside solutions from entity reconciliation. Once the entity boundaries are known, we perform concept normalisation.

## Uncertainty Aware Search and Exploration

In the medical sector, a graph can represent drug and patient vertices connected with respective edges representing interactions (drug-drug) or reactions (patient-drug). Other graphs can connect diseases, genetic mutations, or patients with clinical trials and outcomes. When graphs grow, they tend to follow certain trends such as becoming sparse (i.e., with missing connections) and following a typical power law distribution of connections. In graph theory, these are also known as natural graphs.

Traversing natural graphs to detect certain patterns is problematic due to the disproportional amount of computing power necessary to reach abundant information. To address these issues,  we will employ techniques that can efficiently compress graph information into fixed, lower-dimensional spaces such as embeddings. GNNs (which use embeddings) have already showcased breakthroughs in fields such as protein interaction and physics simulations.

We reframe semantic harmonisation as a graph alignment problem rather than a schema transformation one. We will experiment with both supervised and unsupervised methods, such as EMBERT (Entity-rich Medical BERT), FINAL (Fast Attributed Network Alignment), SEU (Simple but Effective Unsupervised entity alignment), and CONE-Align. Furthermore, we will investigate and showcase the domain-efficacy of these models with a focus on multimodal medical/clinical data.

Within an interoperability pipeline, conformalised link prediction can offer a concrete mechanism to quantify and user-bound uncertainties. Systems such as OrbDB and confidence calibrated vector search engines can use conformal scores to recalibrate similarity results and predictions so that the reported confidence matches observed correctness. Thus, we will explore the use of black-box uncertainty estimators to estimate uncertainties with statistical bounds on graph representation learning models.

By democratising the use of responsible AI in healthcare, we aim to attract the attention of clinicians and biomedical researchers to emerging technologies, eliminating data integration concerns that typically take years to solve, while enhancing digitalisation in healthcare, productivity, and trust by shifting focus to the healthcare problems that matter.
