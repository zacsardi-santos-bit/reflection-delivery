# batch-2 — Reflection SWE-Bench delivery (new)

**Tasks:** 1371 passing (offline-graded via nop+oracle, ECR/GAR-imaged). Excludes 3 non-imaged + 10 QC reward-hack tasks from the 1,408 built.

## Language distribution
| language | tasks | % |
|---|---|---|
| python | 459 | 33.5% |
| typescript | 372 | 27.1% |
| rust | 237 | 17.3% |
| java | 109 | 8.0% |
| go | 74 | 5.4% |
| javascript | 58 | 4.2% |
| dart | 20 | 1.5% |
| csharp | 13 | 0.9% |
| cpp | 10 | 0.7% |
| ruby | 7 | 0.5% |
| c | 6 | 0.4% |
| php | 3 | 0.2% |
| kotlin | 2 | 0.1% |
| dotnet | 1 | 0.1% |

## Category distribution
| category | tasks | % |
|---|---|---|
| Software Engineering | 360 | 26.3% |
| Data Processing and ETL | 266 | 19.4% |
| Machine Learning and AI | 191 | 13.9% |
| Debugging and Repair | 171 | 12.5% |
| Systems, Infrastructure, and Operations | 133 | 9.7% |
| Security | 93 | 6.8% |
| Build, Dependency, and Release Management | 77 | 5.6% |
| Data Querying and Databases | 66 | 4.8% |
| Model Training and ML Infrastructure | 7 | 0.5% |
| Mathematics and Formal Reasoning | 4 | 0.3% |
| Scientific Computing and Domain Science | 3 | 0.2% |

## Subcategory distribution
| subcategory | tasks | % |
|---|---|---|
| Feature implementation | 181 | 13.2% |
| Runtime bug repair | 150 | 10.9% |
| File format parsing and serialization | 129 | 9.4% |
| ML serving and deployment | 89 | 6.5% |
| Compilers, interpreters, and programming languages | 77 | 5.6% |
| NLP and language models | 64 | 4.7% |
| Logging, monitoring, and observability | 52 | 3.8% |
| Data validation | 48 | 3.5% |
| Web, API, and networking software | 48 | 3.5% |
| Authentication and authorization | 45 | 3.3% |
| Text processing | 40 | 2.9% |
| Dependency and lockfile resolution | 37 | 2.7% |
| Refactoring and code modernization | 31 | 2.3% |
| Database administration | 26 | 1.9% |
| OS, process, and service management | 26 | 1.9% |
| Security hardening | 22 | 1.6% |
| CI/CD pipelines | 20 | 1.5% |
| Vulnerability analysis | 20 | 1.5% |
| SQL querying | 19 | 1.4% |
| ETL pipelines | 17 | 1.2% |
| Scheduling and automation infrastructure | 17 | 1.2% |
| Tabular transformation | 15 | 1.1% |
| Model inference and prediction | 15 | 1.1% |
| Testing and quality engineering | 14 | 1.0% |
| Streaming data processing | 14 | 1.0% |
| Model evaluation and benchmarking | 14 | 1.0% |
| Containers and orchestration | 12 | 0.9% |
| Networking configuration | 12 | 0.9% |
| Build system configuration | 12 | 0.9% |
| Query optimization | 10 | 0.7% |
| Concurrency and synchronization debugging | 8 | 0.6% |
| Interpretability and model inspection | 8 | 0.6% |
| Storage and filesystem administration | 6 | 0.4% |
| Cryptography | 6 | 0.4% |
| Analytical queries | 6 | 0.4% |
| Porting and migration | 6 | 0.4% |
| Users, permissions, and access control | 5 | 0.4% |
| Release artifacts | 4 | 0.3% |
| Pipeline and orchestration debugging | 4 | 0.3% |
| Distributed training | 4 | 0.3% |
| NoSQL and document stores | 3 | 0.2% |
| Media data processing | 3 | 0.2% |
| Configuration repair | 3 | 0.2% |
| Test failure repair | 3 | 0.2% |
| Shell and environment configuration | 3 | 0.2% |
| Scripting and automation | 2 | 0.1% |
| Container builds | 2 | 0.1% |
| Build failure repair | 2 | 0.1% |
| Graph and semantic queries | 2 | 0.1% |
| Cross-compilation and platform targeting | 2 | 0.1% |
| Training loops | 2 | 0.1% |
| Symbolic computation | 2 | 0.1% |
| Performance debugging | 1 | 0.1% |
| Version control and repository operations | 1 | 0.1% |
| Fine-tuning | 1 | 0.1% |
| Statistical modeling | 1 | 0.1% |
| Feature engineering | 1 | 0.1% |
| Differential equations and simulation | 1 | 0.1% |
| Algorithms and optimization theory | 1 | 0.1% |
| Numerical methods | 1 | 0.1% |
| Computational linear algebra | 1 | 0.1% |

## Eval pass results (Opus 4.8 — passing / delivered trajectories per task)
| pass/total | tasks | % |
|---|---|---|
| 0/8 | 670 | 48.9% |
| 1/8 | 214 | 15.6% |
| 2/8 | 97 | 7.1% |
| 3/8 | 109 | 8.0% |
| 4/8 | 281 | 20.5% |

## Layout
```
batch-2/
  tasks/<id>/  task.toml, instruction.md,
               environment/{Dockerfile, problem_statement.md (original), prompt_statement.md (rich agent prompt)},
               solution/{solve.sh,golden.patch}, tests/{grade.py,config.json,test.sh}
  evals/<id>/*.json   # Opus-4.8 rollout trajectories
  logs/<id>/          # nop+oracle behavioral logs
```

