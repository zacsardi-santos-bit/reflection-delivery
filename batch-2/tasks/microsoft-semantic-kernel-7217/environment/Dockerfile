FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/microsoft/semantic-kernel.git && \
    git fetch --depth 30 origin e02770d83ef88e1a9213eed6be499c95185eb143 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/python

RUN pip install --upgrade pip setuptools wheel poetry-core

RUN pip install \
    "pydantic==2.8.0" \
    "pydantic-settings>=2.0,<3.0"

RUN pip install \
    "aiohttp>=3.8" \
    "defusedxml>=0.7.1" \
    "numpy>=1.25" \
    "openai>=1.0" \
    "jinja2>=3.1.3" \
    "nest-asyncio>=1.6.0" \
    "pybars4>=0.9.13" \
    "PyYAML>=6.0" \
    "prance>=23.6.21.0" \
    "openapi-core>=0.18,<0.20"

RUN pip install \
    "azure-ai-inference==1.0.0b3" \
    "azure-core>=1.28.0" \
    "azure-identity>=1.13.0" \
    "azure-cosmos>=4.7.0"

RUN pip install \
    "pytest>=8.2.1" \
    "pytest-asyncio>=0.23.7" \
    "pytest-cov>=5.0.0"

RUN pip install -e . --no-deps

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
