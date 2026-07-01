# Reflection SWE-bench Delivery

Clean delivery: **1500 tasks** (harbor format), all QC + nop/oracle validated. Down-sampled from 2,104 by removing 604 go tasks with Opus-4.8 pass@8=0/8 to reduce the go share.

## Pass rate
- nop+oracle (Modal, authoritative): 100% (nop=0/oracle=1).
- CodeBuild login-shell validation: see REQUIREMENTS_RECONFIRM.md.
- LLM semantic QC: PASS_CLEAN + FIXABLE kept; 12 REMOVE excluded.

## Language
| lang | n | % |
|---|--:|--:|
| go | 608 | 40.5% |
| rust | 351 | 23.4% |
| python | 204 | 13.6% |
| typescript | 160 | 10.7% |
| javascript | 81 | 5.4% |
| java | 64 | 4.3% |
| cpp | 11 | 0.7% |
| kotlin | 7 | 0.5% |
| ruby | 6 | 0.4% |
| swift | 3 | 0.2% |
| unknown | 3 | 0.2% |
| csharp | 1 | 0.1% |
| c | 1 | 0.1% |

## Category
| category | n | % |
|---|--:|--:|
| Software Engineering | 936 | 62.4% |
| Debugging and Repair | 220 | 14.7% |
| Security | 82 | 5.5% |
| Systems, Infrastructure, and Operations | 69 | 4.6% |
| Data Processing and ETL | 61 | 4.1% |
| Build, Dependency, and Release Management | 45 | 3.0% |
| Data Querying and Databases | 40 | 2.7% |
| Machine Learning and AI | 22 | 1.5% |
| Scientific Computing and Domain Science | 12 | 0.8% |
| Mathematics and Formal Reasoning | 7 | 0.5% |
| Model Training and ML Infrastructure | 6 | 0.4% |

## pass@8 (Opus-4.8, from 8 trajectories)
| pass@8 | n |
|---|--:|
| 0/8 | 1190 |
| 1/8 | 79 |
| 2/8 | 81 |
| 3/8 | 69 |
| 4/8 | 81 |

All tasks satisfy the Reflection difficulty bar (pass@8 ≤ 0.5, i.e. ≤ 4/8). The lone 5/8 outlier
(`mattermost-mattermost-mobile-8735`) was replaced by a validated 0/8 task (`akuity-kargo-3151`).

## Provenance & caps
- Repo <=10%: max 27 (1.8%), 721 distinct repos — PASS.
- Owner <=20%: max 4.5% — PASS.
- Repos >1000 stars: 83.9% (need >=40%) — PASS.
- Distinct languages: 13 (need >=8) — PASS.
- All 1500/1500 tasks from the approved 2,500-repo set.

## QC
All QC across every layer is in **`QC_REPORT.md`** (single detailed report). Per-task signals: `qc_sot/ALL_TASKS_QC.csv` + `qc_sot/per_task/<id>.md`.

## Layout
- `tasks/<id>/` harbor task · `logs/harbor/<id>/epoch_0/{nop,oracle}/` full harbor run logs · `qc_sot/` full QC source-of-truth + per-task report · `QC_REPORT.md` · `REQUIREMENTS_RECONFIRM.md` · `DATASET_DISTRIBUTION.md`.
