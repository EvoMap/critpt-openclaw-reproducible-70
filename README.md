# CritPt OpenClaw Reproducible 70

[![Strategy Genes arXiv](https://img.shields.io/badge/arXiv-2604.15097-b31b1b.svg)](https://arxiv.org/abs/2604.15097)

This private repository contains a reproducible 70-task **CritPt** result produced with **OpenClaw + Evolver**.

OpenClaw generated task answers, while Evolver supplied and iteratively refined gene-guided solving strategies throughout the workflow.

## At a Glance

- Benchmark: `CritPt`
- Task count: `70`
- Reproduced accuracy: `27.14%`
- Runtime stack: `OpenClaw + Evolver`
- Fixed base model in the cited result: `Gemini 3.1 Pro Preview`

This repository corresponds to the CritPt result reported in *From Procedural Skills to Strategy Genes: Towards Experience-Driven Test-Time Evolution* for **Evolver (Gene) 2026-03-26**, built on top of **Gemini 3.1 Pro Preview** as the fixed base model.

## References

Relevant papers:

- CritPt benchmark: [Probing the Critical Point (CritPt) of AI Reasoning: a Frontier Physics Research Benchmark](https://arxiv.org/abs/2509.26574)
- Strategy-gene / Evolver report: [From Procedural Skills to Strategy Genes: Towards Experience-Driven Test-Time Evolution](https://arxiv.org/abs/2604.15097)

## Evaluation Reference

For broader third-party model benchmarking, see [Artificial Analysis](https://artificialanalysis.ai/). Its Intelligence Index includes `CritPt` among the covered evaluations. After obtaining access to the relevant model or provider API, you can follow its published evaluation methodology and guidance to run comparable assessments.

## Repository Layout

- `results/run_summary.json`: run-level metadata and summary statistics
- `results/submissions/`: generated task submissions
- `results/evaluations/original_aggregate_report.json`: original aggregate evaluation
- `results/evaluations/reproduced_aggregate_report.json`: reproduced aggregate evaluation

## Reproduced Result

The reproduced evaluation matches the original evaluation:

- Total submissions: `70`
- Accuracy: `27.14%`
- Server timeout rate: `0.00%`
- Judge error count: `0`
