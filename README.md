# CritPt OpenClaw Reproducible 70

This private repository contains a reproducible 70-task CritPt submission set produced with **OpenClaw + Evolver**.

The workflow was:

1. OpenClaw generated solutions for the CritPt tasks.
2. Evolver supplied and refined gene-based guidance for task solving.
3. Failed tasks were rerun and merged into a complete 70-task submission set.
4. The final merged set was evaluated again and reproduced the same official score.

## Benchmark

CritPt paper:

- [Probing the Critical Point (CritPt) of AI Reasoning: a Frontier Physics Research Benchmark](https://arxiv.org/abs/2509.26574)

## What is included

- `analysis/critpt_openclaw_gene_accuracy_token_analysis_20260326.md`
  - Original analysis note for the merged evaluation run.
- `results/generations/merged_eval_20260326_091812/run_summary.json`
  - Summary of the final merged 70-task submission set.
- `results/generations/merged_eval_20260326_091812/submissions.__grading_sanitized__/`
  - The 70 sanitized answer files used for official grading.
- `results/evaluations/original/aggregate_report.json`
  - Original official evaluation result.
- `results/evaluations/reproduced/aggregate_report.json`
  - Reproduced evaluation result, rerun later with the same 70-task submission set.
- `artifacts/critpt_openclaw_merged_eval_20260326_091812_bundle.zip`
  - Convenience bundle of the submission package and related files.

## Reproduced result

Both the original evaluation and the reproduced evaluation report the same result:

- Total submissions: `70`
- Accuracy: `27.14%`
- Server timeout rate: `0.00%`
- Judge error count: `0`

## Note

This repository is intended as a compact archival record of a reproducible CritPt result generated through an OpenClaw + Evolver workflow with gene-guided task solving.
