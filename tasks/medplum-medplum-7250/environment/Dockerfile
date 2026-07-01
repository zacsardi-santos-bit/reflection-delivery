FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/medplum/medplum.git && \
    git fetch --depth 30 origin 489903157a43ff031378d56e5d5dc18b3252514a && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

ENV NODE_OPTIONS=--max-old-space-size=8192

RUN npm ci 2>&1 | tail -5

RUN npm run build --filter=!@medplum/docs --filter=!./examples/* 2>&1 | tail -20

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
