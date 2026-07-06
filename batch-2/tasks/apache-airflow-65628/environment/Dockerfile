FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libssl-dev libffi-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin b90710803a45d015a221b6045295d0dc9ddb964f && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install uv

RUN uv pip install --system \
    -e . \
    -e ./airflow-core \
    -e ./task-sdk \
    -e ./providers/common/compat \
    -e ./providers/common/io \
    -e ./providers/common/sql \
    -e ./providers/smtp \
    -e ./providers/standard \
    -e ./providers/git \
    -e "./devel-common[pytest]"

ENV AIRFLOW_HOME=/workspace/airflow_home
ENV AIRFLOW__DATABASE__SQL_ALCHEMY_CONN=sqlite:////workspace/airflow_home/airflow.db
ENV AIRFLOW__CORE__LOAD_EXAMPLES=False
ENV AIRFLOW__CORE__UNIT_TEST_MODE=True

RUN mkdir -p /workspace/airflow_home


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

# --- offline-harbor: satisfy the provider-dependencies guard at BUILD time ---
# At pytest collection the tests_common plugin (and dev/breeze utils) run
#   uv run scripts/ci/prek/update_providers_dependencies.py
# whenever generated/provider_dependencies.json OR its .sha256sum is missing, or the sha256
# of all providers/*/{pyproject.toml,provider.yaml} differs from the stored sum. That uv run
# pulls a PEP-723-pinned CPython 3.10 + deps from the network and fails offline (DNS).
# Fix (internet available at build): generate the JSON once, then write the .sha256sum using
# the SAME hash the plugin computes. With both files present AND the hash matching, the guard
# takes the no-op branch at test time and never invokes uv/network.
RUN cd /workspace/repo && \
    uv run scripts/ci/prek/update_providers_dependencies.py 2>&1 | tail -20 && \
    python3 - <<'PYHASH'
import hashlib
from pathlib import Path
root = Path("/workspace/repo")
files = []
in_ws = False
for line in (root / "pyproject.toml").read_text().splitlines():
    t = line.strip()
    if not in_ws and t.startswith("[tool.uv.workspace]"):
        in_ws = True
    elif in_ws:
        if t.startswith("#"):
            continue
        if t.startswith('"'):
            path = t.split('"')[1]
            if t.startswith('"providers/'):
                files.append(root / path / "pyproject.toml")
                files.append(root / path / "provider.yaml")
        elif t.startswith("]"):
            break
h = hashlib.sha256()
for f in sorted(files):
    h.update(f.read_bytes())
(root / "generated" / "provider_dependencies.json.sha256sum").write_text(h.hexdigest())
print("wrote provider_dependencies.json.sha256sum:", h.hexdigest())
PYHASH

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
