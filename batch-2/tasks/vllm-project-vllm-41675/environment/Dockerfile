FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential cmake ninja-build g++ \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/vllm-project/vllm.git && \
    git fetch --depth 30 origin 2d6b3489b9a325988ad52507236409747d2098a7 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel setuptools-scm packaging jinja2 2>&1 | tail -5

RUN pip install torch==2.11.0+cpu --index-url https://download.pytorch.org/whl/cpu 2>&1 | tail -5

RUN pip install -r requirements/common.txt 2>&1 | tail -10

RUN VLLM_TARGET_DEVICE=empty pip install -e . --no-build-isolation 2>&1 | tail -10

RUN pip install pytest ray tblib anthropic cloudpickle openai httpx msgspec pyzmq 2>&1 | tail -5

RUN printf 'import pytest\n' > /workspace/repo/tests/conftest.py

RUN printf 'import pytest\n' > /workspace/repo/tests/distributed/conftest.py

RUN printf 'def ensure_model_parallel_initialized(*args, **kwargs): pass\ndef init_test_distributed_environment(*args, **kwargs): pass\ndef multi_process_parallel(*args, **kwargs): pass\n' > /workspace/repo/tests/utils.py

ENV PYTHONPATH=/workspace/repo:$PYTHONPATH

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
