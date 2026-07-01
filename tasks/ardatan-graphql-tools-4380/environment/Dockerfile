FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ardatan/graphql-tools.git && \
    git fetch --depth 30 origin ddbeaaf87ddfe0443c916ea86fb7dabd8072b1f6 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

ENV HUSKY=0
ENV CI=true

RUN yarn install --frozen-lockfile --ignore-scripts 2>&1 | tail -20

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
RUN { printf %s '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'; } | base64 -d | gzip -d > /root/_offline_golden.patch && { printf %s 'H4sIAAAAAAACA9VWTW/aQBC98ysmJ0wMNgQrCVQ0EUlbtarSREnUaxZ7wNvYXsu7zocq/ntn1zG4qaFQKlX1wazx7sybN29mHPDpFDqdGVfA3JT592yG0s0Vj6SrUCrpplmeoKPXjpIw2WBTgycBPgHrepOe53cHjjPoHfc9nx0fQa/bPfS8RqfT2chfw7btzXyenkLnsNs+BJvu/QOg5wCln/EJWk2z9doPMWbNNlgtGL2F7w3QFz6l6CsrQ5lHypmhunlO6cRtkksMmq2Wo8QYz3HKEwys1ht9aK5/GjaA9m01M4zFA0rIzRHwc6lEDNJnEcsq3mztzReJVPROI4ERTHIeBQUuy92HDxlLw6vPsO/CXbEfXuzAmbF6bR4advlSEVa4yjF7Lh3oayrEEMZCRMiS8t95sbgj6BUgRdQEpEKQVaArN67gp4qnZOmW0r7gyS54spc8BYJISoT2qgmr5QseQ0xA3vO0SIB2Ji8JHE9mIFGBEqCyHP9fXttLi/VhDk2A7WLT/A/ScF6TBFghVp6kuQIx+UbG5evS2ILUF4YKc0VMZEXXZM871kXZO/La/e6/q0oepxHGmCimOIUFYkpgFWZT5qPcXU31ghEpZsbfEK5FjDqGim7K5QKH2fRx8VRVHscoICMqI4HUmDDuSxfLWOWuFkORR4HW5W5GFxWyRe9ZK/sKsPXN5xfdK5FChA8YlaKHR65CaksF+L9QAz9JQVfAgTdoe2AfDAbtvrdRBaytgaLk9Xp1HdR1XePLoCP1Z8ApdGpkhpOQKYiZ8kNaIhERUW53r4mAZ4SfU6c/nVCnFQl8GX96d3ZTI9taNT2wKMffSXTJxmqFGvfbGt66+xdL14VzUeGbJc+FPUNyyKSh2CBa8FMlOBAXQhlh65i+kjbHLBvC5VIo7016KAWWtjssqb9gcTFHqkkDqjKVZwns7enNDpPqQgR44ix8yxOHxBNY9P1Hx7hyErLjGIZgNBpBc6IHy8sQmm9ewq/G3GK21cZXHXYbjLsV2l/bMKqCWD0oze0HZG1fOhMLAAA='; } | base64 -d | gzip -d > /root/_offline_test.patch && set +e; cd /workspace/repo; \
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
