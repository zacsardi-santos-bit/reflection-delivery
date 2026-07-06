FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV NODE_ENV=test
ENV ELECTRON_SKIP_BINARY_DOWNLOAD=1
ENV CI=true

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    libusb-1.0-0-dev libudev-dev \
    && rm -rf /var/lib/apt/lists/*

RUN install -dm 755 /etc/apt/keyrings \
    && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_20.x nodistro main" > /etc/apt/sources.list.d/nodesource.list \
    && apt-get update \
    && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g pnpm@8.14.1

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/LedgerHQ/ledger-live.git && \
    git fetch --depth 1 origin 1502f64db4baa80820a9d8009de9ca6c0c55b9c8 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN pnpm i --filter="ledger-live-desktop..." --filter="@ledgerhq/hw-transport-mocker..." --filter="ledger-live" --unsafe-perm 2>&1 | tail -30

RUN pnpm turbo build --no-daemon --filter="ledger-live-desktop^..." --filter="@ledgerhq/hw-transport-mocker" 2>&1 | tail -30

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

# OFFLINE-SIZE: keep .git < 100MB by dropping large binary/media blobs from the object store.
# Commit/tree objects and all small source blobs are retained, so HEAD stays == base_commit,
# git remains present/valid (rev-parse/rev-list work) and source checkout/apply still function.
RUN cd /workspace/repo 2>/dev/null && [ -d .git ] && { \
      git repack -ad 2>/dev/null || true; git reflog expire --expire=now --all 2>/dev/null || true; \
      git gc --prune=now 2>/dev/null || true; \
      if [ "$(du -sm .git 2>/dev/null | cut -f1)" -ge 100 ]; then \
        git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype) %(objectsize)' > /tmp/_ao 2>/dev/null; \
        for TH in 1000000 300000 150000 80000 40000 20000; do \
          awk -v th=$TH '!($2=="blob" && $3>th){print $1}' /tmp/_ao > /tmp/_keep; \
          git pack-objects --stdout < /tmp/_keep > /tmp/_np.pack 2>/dev/null || break; \
          rm -f .git/objects/pack/*; mv /tmp/_np.pack .git/objects/pack/pack-shrunk.pack; \
          ( cd .git/objects/pack && git index-pack pack-shrunk.pack >/dev/null 2>&1 ); \
          [ "$(du -sm .git 2>/dev/null | cut -f1)" -lt 90 ] && break; \
        done; \
        rm -f /tmp/_ao /tmp/_keep; \
        git reflog expire --expire=now --all 2>/dev/null || true; git gc --prune=now 2>/dev/null || true; \
      fi; \
    } || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
