FROM node:18-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/microsoft/fluentui-react-native.git && \
    git fetch --depth 30 origin ada08c57f8d07c1be31031ea3d8441faee9fce1c && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN yarn install --frozen-lockfile --ignore-scripts --ignore-engines 2>&1 | tail -20

RUN cd /workspace/repo/scripts && node just-scripts.js ts 2>&1 | tail -30

RUN cd /workspace/repo/packages/utils/test-tools && /workspace/repo/node_modules/.bin/just-scripts ts:commonjs 2>&1 | tail -30

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
RUN { printf %s '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'; } | base64 -d | gzip -d > /root/_offline_golden.patch && { printf %s 'H4sIAAAAAAACA81UUW/iRhB+968Y+R6AGmMTIFIvEHG6qtWdkss16bUPVUUWe2z2YnbR7kKgEf+9s7Zx7CR3l6pRWx6QPTP+dr5vZ76YJwn4fsoNsGDFohuWog4iuVxJgcLo4IM0POERM1yKQKsomM0MaqNns0aqZ4M9o7cwfxEYR+AtJDxDWMoYoR+Gx8Ohw0WMWwgPv14vYcPRMYswcXzfhyDGTSDWWeZ4nvdSjUyn4IfdELx+d/A9TKeOxwlMGfgOmIZLZJGBRMkltJR9bp1UBXdQR4R9WdbrNU6qfZAjKiSSClUd1Lfd+IdM44hogdHNZZ55K4Xm2qCIdt1DvMhUZ0+TbE0yrLlf4ApqYYNBDm+kzLTFdrwYdaT4HNutBoNKRsgla3Wh3YHJKdw5HgA3D6pjTNg6M80qIBChCUAhwqQiW6QAehH1ZbB9eAcYNzA3THEmzOSutVJ8ydSutQeC+AW3ZuJ+ErF0T+8/BThnPIM3KloQybiG2biA0+4h06naMPL91cWHduekiOB2hZFp26Y7lDtnJlpcCbbSC2nKon0nV+4JHTTdFc1xQZWLFGKJGoQ0wMWGZTwmwqDNLqPcI7GevN1KnqL231WrEquS7ah8+pYQCssR1jQESpGi2e4LhAvK/3+e9i/+BxY6m+lyjL5oQD1b8ffN7LnIzzTa0REOB3McvYjRPru3mveO+rn3BgG8pxI4bB9s+l1YGLPSr4MglbKXZkEyf/Pz2Uc7hLS35JL69+uv2Rg85VrQv/6D/Ona8ca/cry1F25XFCflnF7MP9P8HqYWwKVFTsU7g0vtvgY3InRUbjU67pwUSpVci/itzKSyNfpmNyc3rhdJRVN/yWK+tij9o/tUkuHWhpqRH7jdImrb4il5W8P6vNbEakeuQZZh8vPoltCfo7lFFLXKFYtjsh6LflxG9/mAO16+BGO7R6UPZhlfaf4nntOsTFxDi+QWGbFezlFdJGdcoJ7chfsi3NDssWp0enSQ49XgaNAfhPeNPcW6jP1kuT6ME9Mf2ZJnO4t2halE+PTOfVhyRd3XqVaJ35Cni1yoYdjsghwl5VbisIruazqVTvHIVcaBFe4/l/DFZBl9W5YqeoaJ/WYw/Jpi1pkbQo0Du2v0cH3i/AUan15qmQoAAA=='; } | base64 -d | gzip -d > /root/_offline_test.patch && set +e; cd /workspace/repo; \
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
