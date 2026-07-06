FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/continuedev/continue.git && \
    git fetch --depth 1 origin d2a0db01ebb0e5ad861a2fdb1c3dd1f68236ed5f && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo/packages/config-yaml

RUN npm ci 2>&1 | tail -5

RUN npm run build 2>&1 | tail -5

WORKDIR /workspace/repo/core

RUN npm ci 2>&1 | tail -5

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH

# dv01-blobdrop: shrink .git<100MB by dropping blob objects (working tree kept; grade uses patch -p1)
RUN cd /workspace/repo && \
    git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_nb.txt && \
    ( cat /tmp/_nb.txt | git pack-objects --non-empty /tmp/_gcpk ) && \
    rm -f .git/objects/pack/* && cp /tmp/_gcpk-*.pack /tmp/_gcpk-*.idx .git/objects/pack/ && \
    rm -f /tmp/_nb.txt /tmp/_gcpk-* && \
    (git reflog expire --expire=now --all 2>/dev/null || true) && rm -rf .git/logs && \
    (git remote remove origin 2>/dev/null || true) && (git tag -l | xargs -r git tag -d 2>/dev/null || true) && \
    (git gc --prune=now 2>/dev/null || true)

RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
