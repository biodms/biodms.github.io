---
title: 'How the Genomic Variant Store Turns Half a Million Genomes Into a Platform'
slug: how-the-genomic-variant-store-turns-half-a-million-genomes-into-a-platform
date: 2026-07-26
summary: |
  We turn half a million genomes into living infrastructure: a sparse, cloud-native variant store that scales linearly, ingests new samples incrementally, and makes population-scale genomics directly queryable with SQL.
thumbnail: images/thumbnail.png
authors:
  page: people
  names:
    - m-morgan-aster
    - m-kate-balaconis
    - matt-bemis
    - miguel-covarrubias
    - aaron-hatcher
    - christopher-kachulis
    - sofia-labrecque
    - saloni-p-shah
    - lee-lichtenstein
links:
  - icon: fa-regular fa-file-lines
    href: https://openreview.net/forum?id=c8fvDRxcVg
    large: true
    label: Paper
  - icon: fa-solid fa-house
    href: https://gatk.broadinstitute.org/hc/en-us
    large: true
    label: Website
  - icon: fa-brands fa-github
    href: https://github.com/broadinstitute/gatk/tree/ah_var_store
    large: true
    label: Code
---

Sequencing half a million human genomes is an extraordinary scientific achievement. But storing them in a way that lets researchers actually use them — interactively, affordably, at any scale — turns out to be the harder problem.

The All of Us Research Program has sequenced over 535,000 participants (short read whole genome sequencing) as of its most recent data release (CDR v9, June 2026). That's a dataset spanning 1.3 billion SNP/Indel variant sites, built through joint variant calling: a process that filters variants based on read evidence across samples. Joint calling also merges all of the sample variants into one callset. But it introduces two compounding infrastructure headaches:

**The storage problem.** Standard genomic file formats like Variant Call Format (VCF) encode every genomic position for every sample. That means storage grows superlinearly — not just with sample count, but with the number of variant sites discovered as the cohort grows. At biobank scale, full VCF storage is quite large and, for many researchers, prohibitively expensive to analyze. Most variant formats (including VCF) can be read directly by downstream tools ("analysis-ready"), but this forces variant formats to be both inexpensive to store and to be analysis-ready.

**The "N+k problem."** When k new samples arrive, re-running the entire analysis on all N+k samples is wasteful. For a continuously enrolling cohort, that would mean re-doing hundreds of thousands of samples' worth of compute every time a new batch arrives.

The Genomic Variant Store (GVS) solves both.

## A Different Way to Store Variants

GVS is a cloud-native variant database running on Google BigQuery. Its core insight is simple: in any human genome, the vast majority of positions are identical to the reference. Rather than storing every position for every sample, GVS stores only positions where at least one sample carries an alternate allele — a sparse representation that causes storage to scale linearly with cohort size rather than superlinearly.

GVS decouples the storage problem from being analysis-ready. GVS extracts the variants into analysis-ready formats, such as VCF and PLINK pgen. Hail Variant Dataset (VDS) is also supported. GVS includes a workflow for generating these analysis-ready formats on the entire callset or on slices of the data (i.e., filter on genomic regions and/or samples). This has the added bonus of pre-filtering data from the callset that your downstream researchers are not going to analyze.

The schema is designed around this: a variant evidence table (`vet_*`) stores one row per sample per alternate-allele site; a companion table (`ref_ranges_*`) records reference blocks where individual samples are homozygous. Because GVS uses BigQuery, a columnar data warehouse, queries touch only a few columns: position, filter label, and gene symbol. Scanning only those columns reduces per-query costs.

On top of this, GVS includes a Variant Annotation Table (VAT) that stores variant-to-functional annotations, such as gene symbol, protein change, ClinVar significance, consequence, and population allele frequencies (both gnomAD and the instance of GVS). This makes the entire callset queryable via standard SQL, without any specialized genomics tooling.

## Incremental Ingestion and Quality Filtering

GVS solves the N+k problem by treating ingestion as an append operation. When new samples arrive, their gVCFs are converted to sparse alt-allele representation and added to the existing tables. Already-ingested samples are detected and skipped.

After ingestion, quality filtering is handled by GATK VETS (Variant Extract-Train-Score), an isolation-forest outlier detection model that replaced the traditional VQSR approach. VETS is fully shardable, runs 13× faster than VQSR, and uses allele-specific annotations to separate true variants from sequencing noise. When new samples are added, VETS is retrained from scratch on the full, updated callset. This may sound expensive, but model training accounts for only ~15% of total pipeline cost, making full retraining the operationally simpler choice. The All of Us CDR v9 release achieved SNP sensitivity of ~0.985 and precision of ~0.999. Of 1.38 billion total variant sites, 71 million (5.1%) were soft-filtered, leaving 1.31 billion passing.

{{< figure src="images/figure1.png" alt="Figure 1" >}}
**Figure 1.** GVS process for loading samples through joint calling and application support. (1) GVCFs are ingested into a BigQuery database with a specialized schema for variants at scale. (2) A filtering model (VETS) is trained to increase precision. (3) Samples and variants can be extracted into an analysis-ready format for consumption by researchers. GVS supports filtering by both genomic regions and samples, to reduce costs. (4) The Variant Annotation Table (VAT) can be rendered. This table includes standard functional annotations, such as gene, protein change, and variant classification. It also includes frequency information from the instance of GVS and gnomAD, separately. (5) Applications (e.g., GUI-based) can query the GVS table for variant information. This table can be joined with the VAT for more powerful queries. Operations and troubleshooting staff can use the GVS tables to quickly query the variants to locate errors (not shown).
{{< /figure >}}

The CDR v9 run processed 535,662 samples in ~12 days at \\$0.035/sample -- versus ~6 weeks at \\$0.067/sample for CDR v8 -- because ~415,000 samples were already ingested and only the new batch required ingestion.

## What the Queryability Unlocks for Operations

Because GVS is a live BigQuery database, it serves operational needs beyond formal data releases. One of the less visible but most valuable aspects of a queryable genomic data system is its impact on operations. By storing genomic data in a structure that can be queried directly, GVS enables rapid investigation of operational and quality-control (QC) questions that inevitably arise during large-scale data production. Engineers can quickly determine why a particular variant was filtered, identify the number of participants carrying a specific variant, or trace the origin of unexpected data discrepancies. These issues — whether related to variant frequencies, filtering behavior across data releases, or failures in downstream analysis tools — can be answered with straightforward SQL queries, eliminating the need to rerun computationally expensive pipelines or provision new analysis environments. At the scale of hundreds of thousands of whole genomes, operations become a major engineering challenge in their own right, and locating the source of errors across a complex network of interconnected workflows represents a substantial ongoing support burden. The ability to rapidly interrogate production data not only accelerates troubleshooting but also improves data quality, reduces operational costs, and shortens the time required to deliver reliable datasets to researchers.

## GVS as a Platform: Two Data Products

The separation between storage and analysis format is what makes GVS a platform rather than a pipeline. GVS has enabled application development on a familiar database backend.

The All of Us Cohort Builder lets researchers define participant cohorts using combined genotypic and phenotypic criteria. Genotypic queries run against the GVS and VAT to identify participants carrying variants of interest by gene, consequence, or ClinVar classification. Researchers can then generate a whole-genome VCF for all matching participants for roughly USD$80 (for ~5,000 of 414,000 samples in CDR v8).

[The All of Us + AnVIL Imputation Service](<https://allofus-anvil-imputation.broadinstitute.org/>) builds on CDR v8 VDS extractions from GVS to provide cloud-native imputation (of both array and low-pass whole genome) backed by the world's largest and most diverse reference panel: >515,000 whole genomes (414,830 AoU + 100,749 CCDG/AnVIL), covering 665 million high-quality sites with 49% non-European ancestry. Neither service requires a separate copy of the callset — GVS's reproducible extraction workflow is the shared foundation. The Imputation Service is available on Terra (a secure, FedRAMP/NIST 800-53 Rev 5 Moderate environment).

## The Bigger Picture

GVS demonstrates that the choice of storage architecture is not just an engineering detail — it determines what downstream science is possible. By decoupling storage from analysis format and making the callset natively queryable, GVS turns a static data release into living infrastructure for interactive research, operational QC, and population-scale services.
