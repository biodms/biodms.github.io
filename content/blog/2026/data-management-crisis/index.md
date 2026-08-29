---
title: 'When a Brain MRI Is More Than an Image: The Data Management Crisis Behind Clinical AI'
slug: when-a-brain-mri-is-more-than-an-image
date: 2026-08-28
summary: |
  We show that trustworthy clinical AI hinges on harmonizing metadata, labels, provenance, and governance across brain MRI repositories, and we propose a lightweight, loss-aware toolkit that makes those differences explicit instead of hiding them.
thumbnail: images/thumbnail.png
authors:
  page: people
  names:
    - tanmoy-debnath
    - miroslaw-narbutt
    - katarzyna-anna-kazimierczak
links:
  - icon: fa-regular fa-file-lines
    href: https://openreview.net/forum?id=iMNEqE0H1g
    large: true
    label: Paper
references:
  data: references.yaml
---

There is still a gap between AI models that perform well in tests and AI systems that can be trusted in clinical practice. The problem is not only the model. It also comes from differences between datasets, inconsistent metadata, unclear labels, non-reproducible preprocessing, different governance rules, and demographic bias.

## More Data Does Not Automatically Mean Better Data

Imagine building one AI system using brain MRI data from several large repositories such as ADNI, OASIS-3, BraTS, IXI, and UK Biobank. Combining more data sounds useful, but these datasets differ in scanners, imaging protocols, patient groups, metadata, and labels. A brain MRI dataset is therefore more than a collection of images: it also includes information about how scans were collected, processed, and labeled. Without this context, it can be difficult to explain why a model performs well.

## When the Scanner Becomes the Diagnosis

MRI repositories use different scanners, imaging settings, and patient populations. These differences can change how images look to an AI model. If patients with a disease are mostly scanned under one setting and healthy participants under another, the model may learn scanner differences instead of real signs of disease. Scanner and acquisition differences can therefore affect what the model is actually learning.

## The Same Label Can Mean Different Things

Different datasets also use labels in different ways. OASIS-3 and ADNI focus on aging and Alzheimer's disease, while BraTS focuses on brain tumors ([LaMontagne et al., 2019](#lamontagne2019); [Menze et al., 2015](#menze2015); [Kanoria et al., 2026](#kanoria2026)). For this reason, *healthy* in an aging study is not the same as *no tumor* in a cancer study. Harmonization must preserve more than a field name: we also need to know what a label means, when and how it was created, what clinical instrument was used, and whether it refers to a patient, visit, or scan. Two labels with the same name may otherwise represent different clinical evidence. Labeling instructions and agreement between reviewers can also affect the final annotations ([Rädsch et al., 2023](#rädsch2023)).

## Governance Is Part of the Data System

Even technically compatible datasets may have different rules for access and use. Some repositories require registration or special agreements, and privacy rules can differ between countries. These restrictions should be recorded with the metadata so researchers can determine what data may be used, under what conditions, and keep the process auditable.

## The Pipeline Is Part of the Scientific Result

MRI preprocessing can include registration, intensity normalization, skull stripping, segmentation, resampling, and cropping. Different choices at these stages can change the data seen by the model. This means two models cannot be compared fairly if they use different preprocessing pipelines: a better result may come from registration, normalization, or segmentation rather than the model itself. For a fair comparison, the data, split, preprocessing, augmentation, and evaluation should remain the same while only the model architecture changes.

Reproducibility also depends on recording software versions, parameters, and processing order ([Bountris et al., 2025](#bountris2025); [Schintke et al., 2024](#schintke2024)). A study may report using FreeSurfer, FSL, or ANTs, but without these details another team may not reproduce the same results. Tools such as BIDS, DataLad, fMRIPrep, sMRIPrep, MRIQC, and federated-learning frameworks address parts of this problem ([Gorgolewski et al., 2016](#gorgolewski2016); [Halchenko et al., 2021](#halchenko2021)). However, no single system currently covers all the metadata, provenance, access, and cross-repository requirements needed for multi-disease brain MRI AI.

## Bias Can Enter Before the Model Is Trained

The participants included in a dataset can influence what an AI model learns. Public MRI datasets do not always represent the wider population. For example, ADNI has historically included many non-Hispanic White, college-educated participants ([Kanoria et al., 2026](#kanoria2026)). Researchers should therefore track demographic representation and model performance across groups. Otherwise, the model may perform poorly on populations that were not well represented in the original data.

## A Small Hammer for a Large Nail

Instead of building another large platform, we propose a simple toolkit for combining metadata from different MRI repositories. It would map repository-specific fields to a common BIDS-extension format using semantic alignment and store the harmonized metadata in a searchable, versioned SQLite index without copying the original MRI files.

The key principle is *loss-aware harmonization*. The system should keep the original value, transformation rule, mapping confidence, reviewer information, and any possible information loss. Unclear mappings should remain visible rather than being treated as fully correct. The first prototype would be evaluated on a small Alzheimer's disease cohort built from ADNI and OASIS-3.

{{< figure src="images/figure1_metadata_harmonization_toolkit.png" alt="Proposed metadata harmonization toolkit and canonical-field provenance trace." >}}
**Figure 1.** Proposed architecture of the metadata harmonization toolkit and provenance trace for a canonical field. The design preserves the source field, original value, transformation rule, mapping confidence, reviewer attribution, and an information-loss flag.
{{< /figure >}}

## How Would We Know It Worked?

We will evaluate the toolkit at three levels. First, **harmonization quality**: how many fields are mapped automatically, reviewed manually, left ambiguous, or rejected as unsafe. Second, **clinical-semantic validity**: whether diagnostic labels, severity measures, phenotype definitions, and time-related variables keep their original clinical meaning. Third, **downstream utility**: whether harmonization improves cohort construction and model transfer, using measures such as missingness, balanced accuracy, AUROC, calibration, and cross-repository generalization.

{{< figure src="images/figure2_validation_framework.png" alt="Three-tier validation framework for the proposed toolkit." >}}
**Figure 2.** Planned three-tier validation framework: harmonization quality, clinical-semantic validity with manual review of ambiguous mappings, and downstream utility measured through cross-repository cohort consistency and classifier performance.
{{< /figure >}}

## The Bigger Picture

Reliable medical AI requires more than better models. Scanner differences, metadata, clinical labels, preprocessing, governance, and population bias can all affect performance and reproducibility. Better harmonization, provenance tracking, and cross-repository evaluation can help show whether models are learning clinically meaningful patterns rather than dataset-specific differences.

Ultimately, the **entire data and processing pipeline** should be treated as part of the scientific result.
