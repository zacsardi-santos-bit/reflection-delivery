FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch python3 make g++ \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ChainSafe/lodestar.git && \
    git fetch --depth 30 origin 4ff29115e3e6a9d208d3b122e71b212b11fc1ebe && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN yarn install --frozen-lockfile

RUN yarn build

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

# >>> offline-node-bake (new4261) >>>
# Bake dep caches/stores + node_modules at build (network on) so the embedded
# test-time install resolves fully OFFLINE. Caches pinned to HOME-independent paths.
ENV npm_config_cache=/root/.npm
ENV YARN_CACHE_FOLDER=/root/.cache/yarn
ENV COREPACK_ENABLE_STRICT=0
ENV CI=true
COPY _offline_golden.patch /root/_offline_golden.patch
COPY _offline_test.patch /root/_offline_test.patch
RUN set +e; cd /workspace/repo; \
    export npm_config_cache=/root/.npm YARN_CACHE_FOLDER=/root/.cache/yarn; \
    mkdir -p "$npm_config_cache" "$YARN_CACHE_FOLDER"; \
    corepack enable 2>/dev/null || true; \
    # detect Yarn Berry (v2+): in-repo .yarn/cache, driven by the project release binary \
    YBERRY=""; [ -f .yarnrc.yml ] && YBERRY=1; [ -n "$(find . -maxdepth 3 -path '*/.yarn/releases/*' -name 'yarn-*.cjs' 2>/dev/null | head -1)" ] && YBERRY=1; \
    # per-task package manager: prefer pnpm>yarn>npm by lockfile presence \
    PM=npm; \
    if   [ -f pnpm-lock.yaml ] || [ -n "$(find . -maxdepth 3 -name pnpm-lock.yaml -not -path '*/node_modules/*' 2>/dev/null | head -1)" ]; then PM=pnpm; \
    elif [ -n "$YBERRY" ]; then PM=berry; \
    elif [ -f yarn.lock ]      || [ -n "$(find . -maxdepth 3 -name yarn.lock -not -path '*/node_modules/*' 2>/dev/null | head -1)" ]; then PM=yarn; \
    elif [ -f bun.lockb ]; then PM=bun; \
    elif [ -f package-lock.json ] || [ -f npm-shrinkwrap.json ]; then PM=npm; fi; \
    echo "OFFLINE-BAKE: detected PM=$PM"; \
    if [ "$PM" = pnpm ]; then export PNPM_HOME=/root/.local/share/pnpm; pnpm config set store-dir /root/.local/share/pnpm/store --global 2>/dev/null; fi; \
    YBIN="$(find . -maxdepth 3 -path '*/.yarn/releases/*' -name 'yarn-*.cjs' 2>/dev/null | head -1)"; \
    do_install() { \
      case "$PM" in \
        pnpm)  pnpm install --frozen-lockfile 2>/dev/null || pnpm install --no-frozen-lockfile 2>/dev/null || pnpm install 2>/dev/null ;; \
        berry) ( [ -n "$YBIN" ] && node "$YBIN" install 2>/dev/null ) || ( corepack yarn install 2>/dev/null ) || ( yarn install 2>/dev/null ) ;; \
        yarn)  yarn install --frozen-lockfile 2>/dev/null || yarn install 2>/dev/null ;; \
        bun)   bun install 2>/dev/null ;; \
        *)     npm ci 2>/dev/null || npm install 2>/dev/null ;; \
      esac; }; \
    # 1) warm BASE graph into pinned caches \
    do_install; \
    # 2) apply test+golden patches, warm the resulting (golden) graph -> caches \
    ( patch -p1 --fuzz=5 -i /root/_offline_test.patch 2>/dev/null || git apply --3way /root/_offline_test.patch 2>/dev/null || true ); \
    ( patch -p1 --fuzz=5 -i /root/_offline_golden.patch 2>/dev/null || git apply --3way /root/_offline_golden.patch 2>/dev/null || true ); \
    do_install; \
    # 3) revert working tree to BASE WITHOUT a broad git-clean (keep build artifacts \
    #    like dist/, node_modules, .yarn cache). Restore tracked files, then delete only \
    #    NEW files the patches introduced (old side == /dev/null) so the solution isn't baked. \
    ( git checkout -- . 2>/dev/null ); \
    for pf in /root/_offline_test.patch /root/_offline_golden.patch; do \
      awk '/^--- \/dev\/null/{g=1;next} /^\+\+\+ /{if(g){p=$2;sub(/^b\//,"",p);print p} g=0}' "$pf" 2>/dev/null; \
    done | while read -r nf; do [ -n "$nf" ] && rm -f "$nf" 2>/dev/null; done; \
    # 4) re-materialize BASE node_modules from the now-complete caches \
    do_install; \
    true
# Force OFFLINE at TEST time so the embedded install can't reach the network.
RUN printf 'offline=true\nprefer-offline=true\naudit=false\nfund=false\nupdate-notifier=false\n' > /root/.npmrc; \
    printf 'yarn-offline-mirror "/root/.cache/yarn"\nyarn-offline-mirror-pruning false\n' > /root/.yarnrc; \
    ( command -v pnpm >/dev/null 2>&1 && pnpm config set store-dir /root/.local/share/pnpm/store --global 2>/dev/null && pnpm config set offline true --global 2>/dev/null && pnpm config set prefer-offline true --global 2>/dev/null ) || true
ENV YARN_ENABLE_NETWORK=false
ENV npm_config_offline=true
ENV npm_config_prefer_offline=true
ENV npm_config_audit=false
ENV npm_config_fund=false
# <<< offline-node-bake (new4261) <<<

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH

# dv01-leak-cleanup: remove baked golden/test patches from image
RUN rm -f /root/_offline_golden.patch /root/_offline_test.patch /tmp/golden.patch /tmp/__golden.patch /tmp/__test.patch 2>/dev/null || true

RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
