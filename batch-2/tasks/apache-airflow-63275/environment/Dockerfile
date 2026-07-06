# syntax=docker/dockerfile:1.4
FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 56c28a5b32320d387467d711da19e358c9e22733 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip

RUN pip install "hatchling>=1.29.0" "GitPython>=3.1.44" flit_core

RUN pip install --no-deps -e task-sdk

RUN pip install "babel>=2.17.0" "fsspec>=2023.10.0"

RUN pip install --no-deps -e providers/common/compat
RUN pip install --no-deps -e providers/common/io
RUN pip install --no-deps -e providers/common/sql
RUN pip install --no-deps -e providers/smtp
RUN pip install --no-deps -e providers/standard
RUN pip install --no-deps -e providers/git

RUN pip install "sqlparse>=0.5.1" "more-itertools>=9.0.0"

RUN pip install --no-deps -e .

RUN pip install -e airflow-core

RUN pip install --no-deps -e devel-common

RUN pip install \
    pytest \
    pytest-asyncio \
    pytest-mock \
    "time-machine>=2.19.0,<3.0.0" \
    pytest-cov \
    pytest-custom-exit-code \
    pytest-icdiff \
    pytest-instafail \
    pytest-unordered \
    pytest-timeouts \
    pytest-rerunfailures \
    requests-mock \
    aioresponses \
    pyyaml \
    rich

RUN cd /workspace/repo && python scripts/ci/prek/update_providers_dependencies.py

RUN <<'PYEOF' python3
import hashlib
from pathlib import Path

root = Path("/workspace/repo")
h = hashlib.sha256()
c = (root / "pyproject.toml").read_text().splitlines()
inw = False
fs = []
for line in c:
    t = line.strip()
    if not inw and t.startswith("[tool.uv.workspace]"):
        inw = True
    elif inw:
        if t.startswith("#"):
            continue
        if t.startswith('"providers/'):
            p = t.split('"')[1]
            fs.append(root / p / "pyproject.toml")
            fs.append(root / p / "provider.yaml")
        elif t.startswith("]"):
            break
for f in sorted(fs):
    if f.exists():
        h.update(f.read_bytes())
(root / "generated" / "provider_dependencies.json.sha256sum").write_text(h.hexdigest())
PYEOF

ENV AIRFLOW_HOME=/workspace/airflow
ENV AIRFLOW_SOURCES=/workspace/repo


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
