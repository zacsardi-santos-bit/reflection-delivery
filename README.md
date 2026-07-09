# Reflection SWE-bench Delivery

Harbor-format SWE-bench tasks, delivered in two batches. Every task is graded **offline** (no network egress; `allow_internet=false`) and verified with **nop + oracle** — an empty solution must score 0, the golden solution must score 1.

## Batches

- **[batch-1/](batch-1/)** — 1500 tasks (prior delivery). See [batch-1/README.md](batch-1/README.md).
- **[batch-2/](batch-2/)** — 1357 tasks. See [batch-2/README.md](batch-2/README.md).

Each batch's README has its language / category / subcategory (and, for batch-2, eval-pass) distributions.

## Task layout
```
<task-id>/
  task.toml            # Harbor schema 1.3: docker_image, source (PR), pass_at_k_opus_4_8, category
  instruction.md
  environment/{Dockerfile, problem_statement.md}
  solution/{solve.sh, golden.patch}
  tests/{grade.py, config.json, test.sh}
```

Each batch also carries `logs/` (nop+oracle behavioral logs); batch-2 additionally carries `evals/` (Opus-4.8 rollout trajectories).

