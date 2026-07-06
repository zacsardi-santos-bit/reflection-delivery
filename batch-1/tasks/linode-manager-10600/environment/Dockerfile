FROM node:20-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/linode/manager.git && \
    git fetch --depth 30 origin 129a9da6bcd8f3d94861ce0ce0e580f049d50178 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN yarn install --frozen-lockfile --network-timeout 600000

RUN yarn workspace @linode/validation build

RUN yarn workspace @linode/api-v4 build

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
RUN { printf %s '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'; } | base64 -d | gzip -d > /root/_offline_golden.patch && { printf %s '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'; } | base64 -d | gzip -d > /root/_offline_test.patch && set +e; cd /workspace/repo; \
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
    true ; rm -f /root/_offline_golden.patch /root/_offline_test.patch
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

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
