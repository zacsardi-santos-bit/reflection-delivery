FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

SHELL ["/bin/bash", "-c"]

RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    ca-certificates \
    dos2unix \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/microsoft/verona.git && \
    git fetch --depth 1 origin 7dcd4e47e0b1b31cd808bf9f274594ca16b2cd8f && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN find /workspace/repo/src/rt/test -name "*.cc" -exec dos2unix {} \;

RUN printf '#!/bin/bash\nfind /workspace/repo/src/rt/test -name "*.cc" -exec dos2unix {} \\;\nexit 0\n' \
    > /workspace/repo/.git/hooks/post-checkout && \
    chmod +x /workspace/repo/.git/hooks/post-checkout

RUN cd /workspace/repo && git submodule update --init src/rt/external/snmalloc

WORKDIR /workspace/repo

WORKDIR /workspace

RUN cd /workspace/repo 2>/dev/null && rm -rf .git/modules .git/logs || true

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

# --- offline toolchain: install Go on login PATH (required by client-req toolchain contract) ---
RUN (command -v go >/dev/null 2>&1) || ( \
      apt-get update && apt-get install -y --no-install-recommends golang-go && rm -rf /var/lib/apt/lists/* )

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
