# batch-2 — Reflection SWE-Bench delivery (new)

**Tasks:** 1357 passing (offline-graded via nop+oracle, ECR/GAR-imaged). Excludes 3 non-imaged + 10 QC reward-hack tasks from the 1,408 built, plus the 14 tasks already delivered in batch-1 as `ADDED:` QC-swap replacements (a-h-templ-1251, apache-beam-37956, apache-shardingsphere-29702, babel-babel-17803, kong-insomnia-9920, marimo-team-marimo-8311, microsoft-vscode-312486, mui-material-ui-45304, openai-codex-16745, pandas-dev-pandas-64366, prefecthq-prefect-22041, promptfoo-promptfoo-9580, ray-project-ray-63649, spyder-ide-spyder-23137).

## Language distribution
| language | tasks | % |
|---|---|---|
| python | 453 | 33.4% |
| typescript | 369 | 27.2% |
| rust | 236 | 17.4% |
| java | 108 | 8.0% |
| go | 73 | 5.4% |
| javascript | 56 | 4.1% |
| dart | 20 | 1.5% |
| csharp | 13 | 1.0% |
| cpp | 10 | 0.7% |
| ruby | 7 | 0.5% |
| c | 6 | 0.4% |
| php | 3 | 0.2% |
| kotlin | 2 | 0.1% |
| dotnet | 1 | 0.1% |

## Category distribution
| category | tasks | % |
|---|---|---|
| Software Engineering | 357 | 26.3% |
| Data Processing and ETL | 264 | 19.5% |
| Machine Learning and AI | 189 | 13.9% |
| Debugging and Repair | 168 | 12.4% |
| Systems, Infrastructure, and Operations | 131 | 9.7% |
| Security | 92 | 6.8% |
| Build, Dependency, and Release Management | 77 | 5.7% |
| Data Querying and Databases | 66 | 4.9% |
| Model Training and ML Infrastructure | 7 | 0.5% |
| Mathematics and Formal Reasoning | 4 | 0.3% |
| Scientific Computing and Domain Science | 2 | 0.1% |

## Subcategory distribution
| subcategory | tasks | % |
|---|---|---|
| Feature implementation | 179 | 13.2% |
| Runtime bug repair | 149 | 11.0% |
| File format parsing and serialization | 127 | 9.4% |
| ML serving and deployment | 88 | 6.5% |
| Compilers, interpreters, and programming languages | 76 | 5.6% |
| NLP and language models | 63 | 4.6% |
| Logging, monitoring, and observability | 52 | 3.8% |
| Data validation | 48 | 3.5% |
| Web, API, and networking software | 48 | 3.5% |
| Authentication and authorization | 45 | 3.3% |
| Text processing | 40 | 2.9% |
| Dependency and lockfile resolution | 37 | 2.7% |
| Refactoring and code modernization | 31 | 2.3% |
| Database administration | 26 | 1.9% |
| OS, process, and service management | 25 | 1.8% |
| Security hardening | 22 | 1.6% |
| CI/CD pipelines | 20 | 1.5% |
| SQL querying | 19 | 1.4% |
| Vulnerability analysis | 19 | 1.4% |
| ETL pipelines | 17 | 1.3% |
| Scheduling and automation infrastructure | 16 | 1.2% |
| Model inference and prediction | 15 | 1.1% |
| Tabular transformation | 15 | 1.1% |
| Model evaluation and benchmarking | 14 | 1.0% |
| Streaming data processing | 14 | 1.0% |
| Testing and quality engineering | 14 | 1.0% |
| Build system configuration | 12 | 0.9% |
| Containers and orchestration | 12 | 0.9% |
| Networking configuration | 12 | 0.9% |
| Query optimization | 10 | 0.7% |
| Interpretability and model inspection | 8 | 0.6% |
| Concurrency and synchronization debugging | 7 | 0.5% |
| Analytical queries | 6 | 0.4% |
| Cryptography | 6 | 0.4% |
| Porting and migration | 6 | 0.4% |
| Storage and filesystem administration | 6 | 0.4% |
| Users, permissions, and access control | 5 | 0.4% |
| Distributed training | 4 | 0.3% |
| Pipeline and orchestration debugging | 4 | 0.3% |
| Release artifacts | 4 | 0.3% |
| Media data processing | 3 | 0.2% |
| NoSQL and document stores | 3 | 0.2% |
| Shell and environment configuration | 3 | 0.2% |
| Test failure repair | 3 | 0.2% |
| Build failure repair | 2 | 0.1% |
| Configuration repair | 2 | 0.1% |
| Container builds | 2 | 0.1% |
| Cross-compilation and platform targeting | 2 | 0.1% |
| Graph and semantic queries | 2 | 0.1% |
| Scripting and automation | 2 | 0.1% |
| Symbolic computation | 2 | 0.1% |
| Training loops | 2 | 0.1% |
| Algorithms and optimization theory | 1 | 0.1% |
| Computational linear algebra | 1 | 0.1% |
| Differential equations and simulation | 1 | 0.1% |
| Feature engineering | 1 | 0.1% |
| Fine-tuning | 1 | 0.1% |
| Numerical methods | 1 | 0.1% |
| Performance debugging | 1 | 0.1% |
| Version control and repository operations | 1 | 0.1% |

## Eval pass results (Opus 4.8 — passing / delivered trajectories per task)
| pass/total | tasks | % |
|---|---|---|
| 0/8 | 664 | 48.9% |
| 1/8 | 212 | 15.6% |
| 2/8 | 96 | 7.1% |
| 3/8 | 109 | 8.0% |
| 4/8 | 276 | 20.3% |

## Layout
```
batch-2/
  tasks/<id>/  task.toml, instruction.md,
               environment/{Dockerfile, prompt_statement.md (rich agent prompt)},
               solution/{solve.sh,golden.patch}, tests/{grade.py,config.json,test.sh}
  evals/<id>/*.json   # Opus-4.8 rollout trajectories
  logs/<id>/          # nop+oracle behavioral logs
```
