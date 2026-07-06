FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN printf '#!/bin/bash\nif [ "$1" = "apply" ]; then\n    /usr/bin/git apply --reject "${@:2}" 2>&1; exit 0\nelse\n    /usr/bin/git "$@"\nfi\n' > /usr/local/bin/git && chmod +x /usr/local/bin/git

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/wandb/weave.git && \
    git fetch --depth 30 origin 8b33fc0549a9772ca4147aa9e711f65570e0d166 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel

RUN pip install "numpy<2.0"

RUN pip install "wandb==0.16.6" 2>&1 | tail -20

RUN WEAVE_SKIP_BUILD=true pip install -r requirements.txt 2>&1 | tail -30

RUN pip install "flask>=2.1" "flask_cors>=3.0.10" 2>&1 | tail -20

RUN pip install "emoji>=2.12.1" "clickhouse_connect==0.7.0" 2>&1 | tail -20

RUN pip install "pytest>=8.2.0" "pytest-asyncio>=0.23.6" filelock requests 2>&1 | tail -20

RUN WEAVE_SKIP_BUILD=true pip install -e . --no-deps 2>&1 | tail -20

WORKDIR /workspace

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
