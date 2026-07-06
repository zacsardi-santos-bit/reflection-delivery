FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV CI=true

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential gnupg python3 make g++ \
    && install -dm 755 /etc/apt/keyrings \
    && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_18.x nodistro main" > /etc/apt/sources.list.d/nodesource.list \
    && apt-get update && apt-get install -y nodejs \
    && npm install -g yarn@1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/gatsbyjs/gatsby.git && \
    git fetch --depth 1 origin 40b9c523f6261d90fcd279f901cb35df6684ad61 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

# OFFLINE-SIZE: worktree already checked out on disk; rebuild .git pack with ONLY
# commit+tree objects (drop blobs) so .git < 100MB while HEAD==base + shallow stay intact.
RUN bash -c 'set -e; cd /workspace/repo; \
    git rev-list --objects HEAD 2>/dev/null | while read -r oid _; do \
      ty=$(git cat-file -t "$oid" 2>/dev/null); \
      if [ "$ty" = commit ] || [ "$ty" = tree ]; then echo "$oid"; fi; \
    done > /tmp/_keep_oids; \
    git pack-objects /tmp/_slimpack < /tmp/_keep_oids; \
    rm -f .git/objects/pack/*.pack .git/objects/pack/*.idx .git/objects/pack/*.rev; \
    mv /tmp/_slimpack-*.pack .git/objects/pack/; \
    mv /tmp/_slimpack-*.idx  .git/objects/pack/; \
    mv /tmp/_slimpack-*.rev  .git/objects/pack/ 2>/dev/null || true; \
    rm -f /tmp/_keep_oids; \
    git reflog expire --expire=now --all 2>/dev/null || true; rm -rf .git/logs; \
    git gc --prune=now 2>/dev/null || true; \
    echo "SLIM .git=$(du -sm .git|cut -f1)MB HEAD=$(git rev-parse HEAD)"'

WORKDIR /workspace/repo

RUN yarn install --frozen-lockfile --ignore-scripts

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
