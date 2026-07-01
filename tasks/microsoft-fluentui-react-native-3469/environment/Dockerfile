FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN corepack enable

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/microsoft/fluentui-react-native.git && \
    git fetch --depth 30 origin e82d85894a5678fb27507e65dd16d512db9e162c && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

# OFFLINE-BAKE: pin caches to fixed, absolute, image-persisted locations so the
# cache populated at build time (network) is the exact one the test-time re-install
# reads (the test sandbox has no network). Set before the build install runs.
ENV YARN_CACHE_FOLDER=/root/.cache/yarn
ENV npm_config_cache=/root/.npm

RUN yarn install 2>&1 | tail -20

RUN cd /workspace/repo/scripts && \
    mkdir -p lib/tasks lib/configs/jest && \
    cp src/tasks/clean.js src/tasks/copy.js src/tasks/jest.js src/tasks/ts.js src/tasks/eslint.js src/tasks/depcheck.js lib/tasks/ && \
    cp src/configs/jest/setupEnzyme.js lib/configs/jest/ && \
    /workspace/repo/node_modules/.bin/tsc --skipLibCheck 2>&1 | tail -20

RUN cd /workspace/repo/packages/utils/test-tools && \
    /workspace/repo/node_modules/.bin/tsc \
    --outDir lib-commonjs \
    --module commonjs \
    --target es5 \
    --jsx react \
    --skipLibCheck \
    2>&1 | tail -20


# OFFLINE-BAKE: pre-fetch (into cache + node_modules) the EXACT deps the golden/test
# patch adds to package.json -- absent at base commit, so the test-time re-install would
# otherwise need the network. We add them in the repo (correct resolution) then restore
# the base manifest from git so the repo stays at base_commit (NOT applying the patch).
RUN DEPS="tslib@^2.3.1 @office-iss/react-native-win32@^0.72.0 @react-native/metro-config@^0.72.0 react@18.2.0 react-native@^0.72.0"; cd /workspace/repo && \
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

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
