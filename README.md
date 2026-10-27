# Autonomous Code Review: Hidden-Test Results on GitHub Issues

**Independent study by Adam Shakhabov. Preprint; not peer reviewed.**

Does adding a cross-provider LLM review and author-model revision step change whether AI-generated patches pass hidden tests? This retrospective benchmark compares the initial and final patch from the same workflow against the same hidden evaluator.

[Read the paper (PDF)](paper/manuscript.pdf) · [Download the complete public package](autonomous-code-review-study.zip) · [LinkedIn discussion](https://www.linkedin.com/posts/adamshakhabov_autonomous-code-review-hidden-test-results-ugcPost-7520595275229433856--4em/)

## Main finding

Among **40 completed workflows**, the review-and-revision stage produced **no observed changes in hidden-test pass/fail status**: four workflows passed before and after review, and 36 failed before and after review. Another eight of the 48 planned workflows terminated before a complete evaluator outcome.

| Initial outcome | Final pass | Final fail | Total |
| --- | ---: | ---: | ---: |
| Pass | 4 | 0 | 4 |
| Fail | 0 | 36 | 36 |
| Completed workflows | 4 | 36 | 40 |

The final pass rate was **4/40 (10.0%) among completed workflows** and **4/48 (8.3%) across the planned matrix**, counting incomplete workflows as not-pass for the latter operational metric. The observed completed-case uplift was zero percentage points.

**This result applies to the tested setup. It does not establish that LLM review is useless, that initial and final patches are equivalent, or that human review or pull requests should be removed.**

## Experiment design

- **Corpus:** 12 historical GitHub issues from five repositories: ABP Framework, Kiota, Directus, Strapi, and Wagtail. Five C# issues, five JavaScript issues, and two Python issues.
- **Models:** OpenAI GPT-6.1-Sol and Anthropic Claude Sonnet 5.5, used in both author/reviewer directions.
- **Matrix:** 12 issues × 2 directed model pairs × 2 replicates = 48 planned workflows. These are repeated runs on 12 issue clusters, not 48 independent tasks.
- **Workflow:** a bounded terminal agent writes an initial patch → a read-only model from the other provider reviews it → the author model adjudicates the feedback and may revise the patch.
- **Evaluation:** the same task-specific hidden evaluator assesses the initial and final patch. Agents do not receive hidden tests or evaluator results during the workflow.

## Limitations that matter

The corpus is small, purposively selected, and prior-exposed: the task contexts had appeared in earlier pilot runs. Nine of the 12 task inputs contained only an issue title. All four passes arose in the three tasks with a bounded issue snapshot; this descriptive association is confounded by task identity.

Eight workflows were incomplete. Five had an initial evaluator run whose result was not preserved; three terminated before initial evaluation. Missing initial outcomes are **not recoded as failures**, and the full-planned initial pass rate is unavailable.

Binary test outcomes do not measure every useful review finding, maintainability improvement, security concern, or developer-time effect. There was no randomized human-review comparison. Zero observed transitions do not prove a zero effect on new tasks.

## Public materials and reproducibility

The [release archive](autonomous-code-review-study.zip) contains:

| Material | Location inside the extracted package |
| --- | --- |
| Manuscript PDF, Typst source, bibliography, and figure | `paper/` |
| 48 workflow outcomes and 12 task/provenance records | `data/v8_workflows.csv`, `data/tasks.csv` |
| Sanitized operational/accounting tables and aggregate analysis | `data/` |
| Deterministic numerical reproduction script and expected output | `scripts/reproduce_paper_tables.py`, `expected/paper_numbers.json` |
| Harness implementation and frozen configuration | `code/`, `config/` |
| Protocol, results, reproducibility, and artifact-scope notes | `docs/` |
| File-integrity hashes and citation metadata | `RELEASE_MANIFEST.json`, `CITATION.cff` |

To reproduce the numerical summaries, download the archive to an empty working directory and run with Python 3.12:

```sh
python -m zipfile -e autonomous-code-review-study.zip .
cd autonomous-code-review-study
python scripts/reproduce_paper_tables.py --data data --output derived/paper_numbers.json --check expected/paper_numbers.json
```

This command uses Python's standard library, makes no network or model API calls, and checks the derived results against the released expected output. The public archive's SHA-256 values can be checked using its `RELEASE_MANIFEST.json`.

**Reproducibility scope:** the package supports reanalysis of numerical summaries conditional on the released outcome labels. It does not support independent end-to-end benchmark replay or validation of the hidden-test oracle. Hidden evaluators, third-party source snapshots, dependency caches, raw model trajectories, patches, and evaluator logs are intentionally excluded.

The directly linked PDF is an unchanged copy of `paper/manuscript.pdf` in the release archive.

## Citation and licenses

Use `CITATION.cff` from the archive for citation metadata. The manuscript is an independent preprint; the released package does not claim submission to or acceptance by arXiv.

Study-authored software is MIT licensed. Study-authored documentation, aggregate/content-reduced data, configuration summaries, and manuscript assets are licensed under CC BY 4.0. Full notices are included in the archive. Third-party source snapshots and hidden evaluator assets are not included.

