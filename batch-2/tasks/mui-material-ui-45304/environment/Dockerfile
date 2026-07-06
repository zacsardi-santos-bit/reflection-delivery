FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g pnpm@9.15.4

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/mui/material-ui.git && \
    git fetch --depth 1 origin 620be655d1b49693a816968a5120e9a273432923 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

# OFFLINE-BAKE: pin caches to fixed, absolute, image-persisted locations so the
# cache populated at build time (network) is the exact one the test-time re-install
# reads (the test sandbox has no network). Set before the build install runs.
ENV YARN_CACHE_FOLDER=/root/.cache/yarn
ENV npm_config_cache=/root/.npm

RUN pnpm install --frozen-lockfile --ignore-scripts

RUN pnpm --filter @mui/internal-test-utils build

RUN node -e "const fs=require('fs');['packages/api-docs-builder/package.json','packages/mui-system/package.json'].forEach(f=>{const p=JSON.parse(fs.readFileSync(f,'utf8'));p.type='commonjs';fs.writeFileSync(f,JSON.stringify(p,null,2)+'\n');console.log('Updated',f);})" && \
    git config user.email "build@docker" && \
    git config user.name "Docker Build" && \
    git add packages/api-docs-builder/package.json packages/mui-system/package.json && \
    git commit -m "Add type:commonjs for mocha CJS compatibility"


# OFFLINE-BAKE: force every package manager offline at test time; caches are warm
# from the build install(s) above (yarn-classic via ~/.yarnrc, npm+pnpm via ~/.npmrc,
# yarn-berry via YARN_ENABLE_NETWORK reading its project-local .yarn/cache).
RUN { printf -- '--install.offline true\n--install.check-files false\n--add.offline true\n' >> /root/.yarnrc; } 2>/dev/null; { printf 'offline=true\nprefer-offline=true\naudit=false\nfund=false\nupdate-notifier=false\n' >> /root/.npmrc; } 2>/dev/null; true
ENV YARN_ENABLE_NETWORK=false
ENV CI=true

WORKDIR /workspace

# >>> final-git-hygiene (compliance) >>>
RUN cd /workspace/repo 2>/dev/null && ( \
    B=620be655d1b49693a816968a5120e9a273432923; H=$(git rev-parse HEAD 2>/dev/null); \
    if [ "$H" != "$B" ]; then git reset --soft "$B" 2>/dev/null || true; fi; \
    git remote remove origin 2>/dev/null || true; \
    git tag -l 2>/dev/null | xargs -r git tag -d 2>/dev/null || true; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    git gc --prune=now 2>/dev/null || true; \
    rm -rf .git/logs ) ; true
# <<< final-git-hygiene (compliance) <<<

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
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh

# OFFLINE-SIZE: .git for this shallow-depth-1 checkout is >100MB (large monorepo tree).
# Working tree already holds every file; grading applies patches via `patch -p1` (no git
# objects needed, git apply --3way is only a fallback). Drop tree+blob objects from .git,
# keep only the (shallow) commit object so git rev-parse/rev-list still resolve and .git
# stays <100MB. Runs LAST, after all build-time git operations.
RUN cd /workspace/repo && \
    HEAD_SHA=$(git rev-parse HEAD) && \
    git rev-list HEAD | git pack-objects --quiet /tmp/commitsonly && \
    rm -rf .git/objects/pack && mkdir -p .git/objects/pack && \
    cp /tmp/commitsonly-*.pack /tmp/commitsonly-*.idx .git/objects/pack/ && \
    rm -f /tmp/commitsonly-*.pack /tmp/commitsonly-*.idx && \
    find .git/objects -mindepth 2 -maxdepth 2 -type f -delete 2>/dev/null || true; \
    printf '%s\n' "$HEAD_SHA" > .git/shallow && \
    git remote remove origin 2>/dev/null || true; \
    git reflog expire --expire=now --all 2>/dev/null || true; rm -rf .git/logs; \
    echo "GITMB=$(du -sm .git | cut -f1) HEAD=$(git rev-parse HEAD) NC=$(git rev-list --count HEAD)"
