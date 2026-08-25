<!-- ## Activities

### Pre-workshop

**Goals:** Community seeding and talk proposal submissions.\
**Where:** [Discord server](https://discord.gg/mR7Fmh9JtG) and OpenReview.\
**Submission deadlines:** **April 15th** (exposition talk) and **June 15th** (project talk).\
**Acceptance notifications:** **May 1st** (exposition talk) and **July 15th** (project talk).

### Workshop Day

**Goals:** Presentations, in-person networking, gathering of insights.\
**Where:** [VLDB 2026](https://vldb.org/2026/) in **Boston**, USA.\
**When:** **August 31st** or **September 4th** (TBD).

### Post-workshop

**Goals:** Distilling key conclusions, defining community goals, planning future activities. -->

## Program

**Host conference:** [VLDB 2026](https://vldb.org/2026/) in Boston, USA \
**Date:** September 4th \
**Time:** 13:45 - 18:15 \
**Venue:** The Westin Boston Seaport District, [Grand Ballroom D](https://www.marriott.com/en-us/hotels/bosow-the-westin-boston-seaport-district/events/#floorplans) \
{{< details summary="Floor Plan" >}}
![Floor Plan](https://www.marriott.com/content/dam/marriott-digital/wi/us-canada/hws/b/bosow/en_us/floor-plan/meeting-space/assets/bosowf03.png)
{{< /details >}}
**[<i class="fa-solid fa-calendar-plus"></i> Add to Calendar](https://calget.com/vhr5m7fs)** **[<i class="fa-solid fa-location-dot"></i> Show on Map](https://maps.app.goo.gl/bJgnLS6o3StWpLaW6)**

<!-- The workshop program will feature a selection of keynotes and invited talks given by
speakers from the biomedical and data management communities, along with talks proposed through
submissions, as well as various other networking opportunities. -->

---

### [13:45 - 15:15] **Session 1**

#### [13:45] **Workshop Intro**

#### [13:55] **Keynote**


<!-- **TBD** <br> -->
**[<i class="fa-solid fa-circle-user"></i> Juliana Freire](#juliana-freire)**
<!-- <details><summary>Abstract</summary>TBD</details> -->

#### [14:30] **Submitted talks**

**Mediverse: Multimodal Clinical Exploration and Search on a Single Graph** <br>
**[<i class="fa-solid fa-circle-user"></i> Andra Ionescu](#andra-ionescu)** **[<i class="fa-solid fa-bolt"></i> Lightning talk](#ionescu2026)**

**MedSQLX: Translation of Medical Queries into UDF-Centric SQL** <br>
**[<i class="fa-solid fa-circle-user"></i> Catlynh Nguyen](#catlynh-nguyen)** **[<i class="fa-solid fa-rocket"></i> Project talk](#nguyen2026)**

**An Analysis Graph for Statistical Genetics Agents** <br>
**[<i class="fa-solid fa-circle-user"></i> Stephen Dorn](#stephen-dorn)** **[<i class="fa-solid fa-rocket"></i> Project talk](#dorn2026)**

**Right for the Wrong Reasons: A Benchmark for Hallucination and Clinical Safety in AI Health Triage** <br>
**[<i class="fa-solid fa-circle-user"></i> Sreeram Marimuthu](#sreeram-marimuthu)** **[<i class="fa-solid fa-rocket"></i> Project talk](#marimuthu2026)**

#### [15:05] **Invited talk**

**Brim: A Democratized AI-Guided Chart Abstraction Platform** <br>
{{< details summary="Abstract" >}}
Clinical research, operation and clinical trials rely on accurate and actionable data, yet manual chart review is slow and difficult to scale. This talk introduces Brim: an AI-Guided Chart Abstraction Platform, which combines foundational large language models with structured workflows to assist reviewers in extracting clinical variables from unstructured medical records. Designed to augment human expertise, the platform prioritizes relevant documents, generates candidate abstractions, and streamlines validation. Brim is built for scalable processing of large datasets, allows custom variable extraction definitions, and includes the strong governance controls required for healthcare data. In this talk, you will learn about the Brim infrastructure and deployment use cases from across the nation.
{{< /details >}}
**[<i class="fa-solid fa-circle-user"></i> Daniel Fabbri](#daniel-fabbri)** <br>

---

### [15:15 - 15:45] **Coffee break**

---

### [15:45 - 17:15] **Session 2**

#### [15:45] **Keynote**

<!-- **TBD** <br> -->
**[<i class="fa-solid fa-circle-user"></i> Nils Gehlenborg](#nils-gehlenborg)** <br>
<!-- <details><summary>Abstract</summary>TBD</details> -->

#### [16:15] **Submitted talks**

**The Data Management Crisis Behind AI-Based Brain MRI Diagnosis: Heterogeneity, Governance, and Reproducibility** <br>
**[<i class="fa-solid fa-circle-user"></i> Tanmoy Debnath](#tanmoy-debnath)** **[<i class="fa-solid fa-bolt"></i> Lightning talk](#debnath2026)**

**Scaling Population Genomics to a Million: Genomic Variant Store as a Variant Data Management Engine** <br>
**[<i class="fa-solid fa-circle-user"></i> Aaron Hatcher](#aaron-hatcher)** **[<i class="fa-solid fa-rocket"></i> Project talk](#aster2026)**

**Data Gatherer Revisited: Scalable Dataset Reference Extraction from Biomedical Literature** <br>
**[<i class="fa-solid fa-circle-user"></i> Pietro Marini](#pietro-marini)** **[<i class="fa-solid fa-rocket"></i> Project talk](#marini2026)**

**Similarity-Based Load-Balancing for Distributed Genomic Indices** <br>
**[<i class="fa-solid fa-circle-user"></i> Woodward Galbraith](#woodward-galbraith)** **[<i class="fa-solid fa-rocket"></i> Project talk](#galbraith2026)**

#### [16:50] **Invited talk**

**Embeddable OLAP for Variant Data: Why Warehousing, Search, and Research can share an Engine** <br>
{{< details summary="Abstract" >}}
Population genomic variant data has an awkward dual nature. Most variants in a cohort are rare, but most entries in the variant-by-sample matrix belong to the minority of common variants. The data is typically consumed either as a numeric matrix of common variants or a sparse relational table of rare ones, but the boundary of which variants belong to each domain is query-specific.

Three common categories of usage exist for variant data: warehousing, interactive search, and interchange with downstream analysis tools (CLI bioinformatics tools, statistical models). At scale, interchange between different systems serving these three categories becomes costly and limits the cadence of data publication, because new samples arrive incrementally but the entire dataset gets exported in bulk.

Two key observations unlock systems that support all three patterns without interchange. First, variant search is best served not by large indexes but by a domain-optimized OLAP engine. Second, an embeddable engine allows direct queries of stored data by research tooling. Phoebe is such a system built around the open-source DataFusion and Vortex projects, and a lightweight web server embedding Phoebe is capable of running multiple-gene cohorting search queries against 3.1 million simulated realistic exomes on S3 in under two seconds.
{{< /details >}}
**[<i class="fa-solid fa-circle-user"></i> Tim Poterba](#tim-poterba)** <br>

#### [17:00] **Concluding remarks**

**Plenary discussion: Looking ahead** \
*Flexible duration, after which we transition to the poster session.*

---

### [17:30 - 18:15] **Poster Session**

**Room:** Galleria <br>

{{< details summary="Floor Plan" >}}
![Floor Plan](https://www.marriott.com/content/dam/marriott-digital/wi/us-canada/hws/b/bosow/en_us/floor-plan/meeting-space/assets/bosowf05.png)
{{< /details >}}

---
