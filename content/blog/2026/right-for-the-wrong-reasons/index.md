---
title: 'Right for the Wrong Reasons: A Benchmark for Hallucination and Clinical Safety in AI Health Triage'
slug: right-for-the-wrong-reasons
date: 2026-07-27
summary: |
  We show that accuracy hides the real risks in AI health triage: our benchmark measures faithfulness, calibration, and under-triage, revealing that the most accurate model fabricates its reasoning most often.
thumbnail: images/thumbnail.png
authors:
  page: people
  names:
    - sreeram-marimuthu
    - roee-shraga
    - xiaozhong-liu
    - patricia-l-mabry
links:
  - icon: fa-regular fa-file-lines
    href: https://openreview.net/forum?id=N6oia01YDX
    large: true
    label: Paper
  - icon: fa-brands fa-github
    href: https://github.com/sreerammarimuthu/AI-triage-benchmark
    large: true
    label: Code
---

## The Problem with "Accurate" AI

Over 40 million people use ChatGPT every day, and a growing number of them turn to it for health advice. Consumer-facing systems like ChatGPT Health now recommend triage actions ranging from "monitor at home" to "go to the emergency department now." The stakes are high: a missed emergency recommendation can delay life-saving care, while an over-cautious one floods emergency departments and pulls resources away from those who need them most.

So how good are these systems, really? And is "accuracy" even the right thing to measure?

## A Model Can Be Right for the Wrong Reasons

Most evaluations of AI health systems stop at one question: did the model predict the right triage level? But a model can be right for the wrong reasons, assigning the correct urgency while supporting it with clinical details it invented entirely.

Consider two real cases from our study. A 29-year-old man reports that routine bloodwork came back abnormal. He feels fine, has no bleeding, and takes no medications. One model flagged "an elevated INR suggesting a problem with the coagulation system." INR was never mentioned. The model fabricated that detail to justify its recommendation.

In another case, a 29-year-old man caring for a newborn describes two weeks of emotional distress and frightening thoughts of self-harm. A model attributed his symptoms to "postpartum depression" caused by "hormonal changes." The patient is male. The model invented a biologically impossible diagnosis, reasoned from it confidently, and never noticed the contradiction.

In both cases the model may have reached a defensible triage level. But a patient reading a fabricated explanation is being misled by a system that appears trustworthy but is not. This is what accuracy alone cannot detect.

## What We Built

We constructed a reproducible benchmark evaluating five AI models across 78 physician-labeled clinical vignettes spanning 19 medical domains. Four open-source systems, Mistral 7B, DeepSeek-R1 7B, Gemma 2 9B, and Qwen2.5 7B, were compared against ChatGPT Health as a baseline.

What distinguishes our benchmark is that we measure six dimensions, not just accuracy:

- **Accuracy:** did the model assign the right triage level?
- **Calibration:** does stated confidence match how often the model is actually correct?
- **Faithfulness Rate:** does the explanation stick to what the patient reported, or does it introduce fabricated clinical details?
- **Coherence Rate:** does the model's reasoning logically support the triage level it chose?
- **Under-Triage Rate:** how often does the model recommend less urgent care than needed? This is the dangerous failure mode.
- **Over-Triage Rate:** how often does it recommend more urgent care than needed, wasting resources?

We tested each vignette in two forms: symptoms only, and enriched with vitals, physical exam findings, and lab results. We also tested whether changing the prompt structure shifted model behavior. All predictions are stored in shared checkpoint files, making every result fully reproducible.

## What We Found

**The most accurate model fabricates the most.** ChatGPT Health achieved the highest accuracy at 84.6%, but our automated hallucination checks detected fabricated clinical details in 69.2% of its explanations. It is often correct, but frequently invents the reasoning to get there.

**The most faithful model is the least safe.** DeepSeek-R1 7B had the highest Faithfulness Rate at 55.1%, staying closest to what patients reported. But it also had the lowest accuracy at 50.0% and the highest under-triage rate at 43.6%, including a 66.7% failure rate on emergency-level cases. Staying grounded is not the same as being clinically safe.

**Gemma 2 9B is the strongest open-source option.** It trails ChatGPT Health by only 5% in accuracy (79.5% vs. 84.6%), achieves the best calibration of any model tested, and offers the most balanced safety profile with a 12.8% under-triage rate and 7.7% over-triage rate.

**More clinical data improves accuracy but degrades faithfulness.** When models received full clinical prompts including vitals, exam findings, and lab results, four out of five became more accurate. But every model's Faithfulness Rate dropped, some by more than 60%. Models elaborated on the clinical data rather than grounding their reasoning in it. ChatGPT Health followed the same pattern, confirming this is not a quirk of smaller models.

**Prompt format matters enormously.** Switching to a more constrained prompt structure changed accuracy by 20.5% for DeepSeek-R1 7B and 15.4% for Mistral 7B, in opposite directions. DeepSeek depends on extended chain-of-thought reasoning and suffers when constrained; Mistral's free-form outputs introduce noise that structure suppresses. Gemma 2 9B and Qwen2.5 7B remained comparatively stable.

## Why This Matters Beyond NLP

Clinical AI benchmarks today are inconsistently structured and rarely designed for reproducible multi-model comparison. There is no agreed-upon pipeline for automatically assessing the faithfulness or consistency of model-generated clinical reasoning at scale. Our pipeline addresses this with standardized schemas, checkpoint-based execution, and origin tracking to audit which inputs produced which outputs, structural solutions that make rigorous clinical benchmarking possible at low cost.

## What Comes Next

We are extending the work to cover factorial stress-test conditions including demographic framing and social anchoring bias, testing hallucination mitigation through retrieval-augmented generation and faithfulness-aware fine-tuning, and applying the same framework to a 21-case emergency department dataset where early results suggest information quality matters more than information volume. Once complete, we will release the full pipeline, vignette dataset, judge prompts, and metrics module as a reusable open benchmark.

## The Bottom Line

The most accurate model fabricates clinical details in 69.2% of its explanations. Emergency-level cases are under-triaged at rates as high as 43.6%. Prompt changes can shift accuracy by more than 20%. None of this surfaces in a simple accuracy score. Multi-metric evaluation infrastructure for clinical AI is not optional. It is what responsible deployment requires.

