FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential libffi-dev libssl-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 7f54b63eb9d3211dee72443c204e1975e7060635 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --no-cache-dir \
    -e . \
    -e airflow-core \
    -e task-sdk \
    -e providers/common/compat \
    -e providers/standard \
    -e providers/common/ai

RUN pip install --no-cache-dir \
    pytest pytest-mock pytest-asyncio time-machine numpy pandas kubernetes

RUN python -c "\
import hashlib, json; \
from pathlib import Path; \
root = Path('/workspace/repo'); \
gen = root / 'generated'; \
gen.mkdir(exist_ok=True); \
pf = root / 'pyproject.toml'; \
lines = pf.read_text().splitlines(); \
files = []; \
in_ws = False; \
[files.append(root / l.strip().split('\"')[1] / 'pyproject.toml') or files.append(root / l.strip().split('\"')[1] / 'provider.yaml') for l in lines if (in_ws := in_ws or l.strip().startswith('[tool.uv.workspace]')) and l.strip().startswith('\"providers/')]; \
h = hashlib.sha256(); \
[h.update(f.read_bytes()) for f in sorted(files) if f.exists()]; \
(gen / 'provider_dependencies.json').write_text('{}'); \
(gen / 'provider_dependencies.json.sha256sum').write_text(h.hexdigest()); \
print('provider_dependencies.json hash:', h.hexdigest()) \
"

ENV PYTHONPATH=/workspace/repo/devel-common/src:/workspace/repo
ENV AIRFLOW_SOURCES=/workspace/repo
ENV _AIRFLOW_SKIP_DB_TESTS=true
ENV _AIRFLOW__AS_LIBRARY=true


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true
# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` (the client
# verifier exec) keeps go/cargo/java/etc on PATH after /etc/profile resets it.
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
