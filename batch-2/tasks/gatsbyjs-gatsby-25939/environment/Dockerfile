FROM node:18-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/gatsbyjs/gatsby.git && \
    git fetch --depth 1 origin 22ed01dc19c0d6ff9d46ba05fac1bb5ad7431b53 && \
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

# OFFLINE-BAKE: pin caches to fixed, absolute, image-persisted locations so the
# cache populated at build time (network) is the exact one the test-time re-install
# reads (the test sandbox has no network). Set before the build install runs.
ENV YARN_CACHE_FOLDER=/root/.cache/yarn
ENV npm_config_cache=/root/.npm

RUN yarn install --frozen-lockfile 2>&1 | tail -20

RUN cd packages/gatsby-core-utils && node_modules/.bin/babel src --out-dir dist/ --ignore "**/__tests__" --ignore "**/__mocks__" --extensions ".ts" 2>&1 | tail -10


# OFFLINE-BAKE: pre-fetch (into cache + node_modules) the EXACT deps the golden/test
# patch adds to package.json -- absent at base commit, so the test-time re-install would
# otherwise need the network. We add them in the repo (correct resolution) then restore
# the base manifest from git so the repo stays at base_commit (NOT applying the patch).
RUN DEPS="csstype@2.6.10 @mdx-js/mdx@2.0.0-next.4 @mdx-js/react@2.0.0-next.4 @mdx-js/runtime@2.0.0-next.4 ink-select-input@4.0.0 remark-mdx@1.6.14 remark-mdxjs@2.0.0-next.4"; cd /workspace/repo && \
    cp package.json /tmp/pkg.bak 2>/dev/null; cp yarn.lock /tmp/yarn.bak 2>/dev/null; \
    cp package-lock.json /tmp/npm.bak 2>/dev/null; cp pnpm-lock.yaml /tmp/pnpm.bak 2>/dev/null; \
    { yarn add --ignore-engines --ignore-scripts -W $DEPS 2>/dev/null || \
      yarn add --ignore-engines --ignore-scripts $DEPS 2>/dev/null || \
      npm install --no-audit --no-fund --legacy-peer-deps $DEPS 2>/dev/null || \
      { command -v pnpm >/dev/null 2>&1 && pnpm add -w --ignore-scripts $DEPS 2>/dev/null; } || \
      { command -v pnpm >/dev/null 2>&1 && pnpm add --ignore-scripts $DEPS 2>/dev/null; } || \
      for d in $DEPS; do yarn add --ignore-engines --ignore-scripts "$d" 2>/dev/null || npm install --no-audit --no-fund --legacy-peer-deps "$d" 2>/dev/null || true; done; }; \
    cp /tmp/pkg.bak package.json 2>/dev/null; cp /tmp/yarn.bak yarn.lock 2>/dev/null; \
    cp /tmp/npm.bak package-lock.json 2>/dev/null; cp /tmp/pnpm.bak pnpm-lock.yaml 2>/dev/null; true

# OFFLINE-BAKE: force every package manager offline at test time; caches are warm
# from the build install(s) above (yarn-classic via ~/.yarnrc, npm+pnpm via ~/.npmrc,
# yarn-berry via YARN_ENABLE_NETWORK reading its project-local .yarn/cache).
RUN { printf -- '--install.offline true\n--install.check-files false\n--add.offline true\n' >> /root/.yarnrc; } 2>/dev/null; { printf 'offline=true\nprefer-offline=true\naudit=false\nfund=false\nupdate-notifier=false\n' >> /root/.npmrc; } 2>/dev/null; true
ENV YARN_ENABLE_NETWORK=false
ENV CI=true

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
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
