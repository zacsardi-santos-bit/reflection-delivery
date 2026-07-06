FROM node:18

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/storybookjs/storybook.git && \
    git fetch --depth 30 origin 3ae39b69e8e8f62b3df9b6e811219590aa088887 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN corepack enable

WORKDIR /workspace/repo/code

RUN yarn install --immutable 2>&1 | tail -30

WORKDIR /workspace/repo/scripts

RUN yarn install --immutable 2>&1 | tail -30

WORKDIR /workspace/repo/code

RUN cd /workspace/repo/code/lib/channels && node --loader /workspace/repo/scripts/node_modules/esbuild-register/loader.js -r /workspace/repo/scripts/node_modules/esbuild-register/register.js /workspace/repo/scripts/prepare/bundle.ts 2>&1 | tail -5

RUN cd /workspace/repo/code/lib/core-events && node --loader /workspace/repo/scripts/node_modules/esbuild-register/loader.js -r /workspace/repo/scripts/node_modules/esbuild-register/register.js /workspace/repo/scripts/prepare/bundle.ts 2>&1 | tail -5

RUN cd /workspace/repo/code/lib/node-logger && node --loader /workspace/repo/scripts/node_modules/esbuild-register/loader.js -r /workspace/repo/scripts/node_modules/esbuild-register/register.js /workspace/repo/scripts/prepare/bundle.ts 2>&1 | tail -5

RUN cd /workspace/repo/code/lib/types && node --loader /workspace/repo/scripts/node_modules/esbuild-register/loader.js -r /workspace/repo/scripts/node_modules/esbuild-register/register.js /workspace/repo/scripts/prepare/bundle.ts 2>&1 | tail -5

RUN cd /workspace/repo/code/lib/csf-tools && node --loader /workspace/repo/scripts/node_modules/esbuild-register/loader.js -r /workspace/repo/scripts/node_modules/esbuild-register/register.js /workspace/repo/scripts/prepare/bundle.ts 2>&1 | tail -5

RUN cd /workspace/repo/code/lib/core-common && node --loader /workspace/repo/scripts/node_modules/esbuild-register/loader.js -r /workspace/repo/scripts/node_modules/esbuild-register/register.js /workspace/repo/scripts/prepare/bundle.ts 2>&1 | tail -5

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
