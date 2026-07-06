FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/langchain-ai/langchain.git && \
    git fetch --depth 30 origin ac41199338b00ba3900bb5ff424a98c54e521d7e && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --pre "fireworks-ai>=1.2.0a71,<2.0.0" 2>&1 | tail -20

RUN pip install -e libs/core 2>&1 | tail -20

RUN pip install -e libs/standard-tests 2>&1 | tail -20

RUN pip install --no-deps -e libs/partners/fireworks 2>&1 | tail -20

RUN pip install "openai>=2.0.0,<3.0.0" "requests>=2.0.0,<3.0.0" "aiohttp>=3.9.1,<4.0.0" httpx 2>&1 | tail -20

RUN pip install "pytest>=9.0.3,<10.0.0" "pytest-asyncio>=1.3.0,<2.0.0" "pytest-mock>=3.10.0,<4.0.0" "freezegun>=1.2.2,<2.0.0" "syrupy>=5.0.0,<6.0.0" "pytest-watcher>=0.3.4,<1.0.0" "pytest-socket>=0.7.0,<1.0.0" "pytest-xdist>=3.8.0,<4.0.0" 2>&1 | tail -20

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
