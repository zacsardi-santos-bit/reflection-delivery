# batch-1 — Reflection SWE-Bench delivery (prior)

**Tasks:** 1500 (prior delivery; Harbor format, offline nop+oracle validated).

## Language distribution
| language | tasks | % |
|---|---|---|
| go | 607 | 40.5% |
| rust | 351 | 23.4% |
| python | 204 | 13.6% |
| typescript | 160 | 10.7% |
| javascript | 81 | 5.4% |
| java | 64 | 4.3% |
| cpp | 11 | 0.7% |
| kotlin | 7 | 0.5% |
| ruby | 6 | 0.4% |
| swift | 3 | 0.2% |
| shell | 2 | 0.1% |
| powershell | 1 | 0.1% |
| csharp | 1 | 0.1% |
| ocaml | 1 | 0.1% |
| c | 1 | 0.1% |

## Category distribution
| category | tasks | % |
|---|---|---|
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

## Subcategory distribution
| subcategory | tasks | % |
|---|---|---|
| Feature implementation | 504 | 33.6% |
| Runtime bug repair | 187 | 12.5% |
| Compilers, interpreters, and programming languages | 129 | 8.6% |
| Refactoring and code modernization | 114 | 7.6% |
| Web, API, and networking software | 111 | 7.4% |
| Testing and quality engineering | 39 | 2.6% |
| Porting and migration | 32 | 2.1% |
| Security hardening | 31 | 2.1% |
| Authentication and authorization | 30 | 2.0% |
| File format parsing and serialization | 27 | 1.8% |
| Dependency and lockfile resolution | 25 | 1.7% |
| Logging, monitoring, and observability | 25 | 1.7% |
| Containers and orchestration | 25 | 1.7% |
| Cryptography | 15 | 1.0% |
| Database administration | 13 | 0.9% |
| Concurrency and synchronization debugging | 11 | 0.7% |
| SQL querying | 11 | 0.7% |
| Build failure repair | 10 | 0.7% |
| ML serving and deployment | 10 | 0.7% |
| Query optimization | 9 | 0.6% |
| ETL pipelines | 8 | 0.5% |
| Tabular transformation | 8 | 0.5% |
| Data validation | 7 | 0.5% |
| Vulnerability analysis | 6 | 0.4% |
| OS, process, and service management | 6 | 0.4% |
| Build system configuration | 6 | 0.4% |
| Numerical methods | 6 | 0.4% |
| Text processing | 5 | 0.3% |
| NLP and language models | 5 | 0.3% |
| CI/CD pipelines | 5 | 0.3% |
| NoSQL and document stores | 5 | 0.3% |
| Streaming data processing | 5 | 0.3% |
| Users, permissions, and access control | 5 | 0.3% |
| Version control and repository operations | 4 | 0.3% |
| Test failure repair | 4 | 0.3% |
| Model inference and prediction | 4 | 0.3% |
| Package publishing | 3 | 0.2% |
| Scripting and automation | 3 | 0.2% |
| Container builds | 3 | 0.2% |
| Shell and environment configuration | 3 | 0.2% |
| Model evaluation and benchmarking | 3 | 0.2% |
| Training loops | 3 | 0.2% |
| Performance debugging | 3 | 0.2% |
| Networking configuration | 3 | 0.2% |
| Computational linear algebra | 3 | 0.2% |
| Release artifacts | 3 | 0.2% |
| Differential equations and simulation | 3 | 0.2% |
| Configuration repair | 3 | 0.2% |
| Analytical queries | 2 | 0.1% |
| Pipeline and orchestration debugging | 2 | 0.1% |
| Distributed training | 2 | 0.1% |
| Number theory and exact arithmetic | 2 | 0.1% |
| Signal processing | 2 | 0.1% |
| Storage and filesystem administration | 2 | 0.1% |
| Evaluation infrastructure | 1 | 0.1% |
| Algorithms and optimization theory | 1 | 0.1% |
| Media data processing | 1 | 0.1% |
| Formal verification | 1 | 0.1% |
| Statistical modeling | 1 | 0.1% |

## Layout
```
batch-1/
  tasks/<id>/  task.toml, instruction.md, environment/{Dockerfile,problem_statement.md},
               solution/{solve.sh,golden.patch}, tests/{grade.py,config.json,test.sh}
  logs/        # nop+oracle behavioral logs
```

