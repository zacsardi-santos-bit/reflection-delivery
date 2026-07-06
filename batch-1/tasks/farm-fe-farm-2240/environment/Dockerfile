FROM ubuntu:24.04

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    libnss3 libnspr4 libatk1.0-0t64 libatk-bridge2.0-0 \
    libcups2t64 libdrm2 libxkbcommon0 libxcomposite1 \
    libxdamage1 libxfixes3 libxrandr2 libgbm1 libasound2t64 \
    libpango-1.0-0 libcairo2 libx11-xcb1 libxcb1 libxext6 \
    libfontconfig1 fonts-liberation libdbus-1-3 \
    && rm -rf /var/lib/apt/lists/*

RUN install -dm 755 /etc/apt/keyrings \
    && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_20.x nodistro main" > /etc/apt/sources.list.d/nodesource.list \
    && apt-get update && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g pnpm@9.4.0

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/farm-fe/farm.git && \
    git fetch --depth 30 origin fffb78bde66d5010a4b349cabb0595e17385ab98 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pnpm install --frozen-lockfile

RUN mkdir -p /tmp/farm-bins \
    && cd /tmp/farm-bins \
    && npm init -y \
    && npm install @farmfe/core-linux-x64-gnu@2.0.0-beta.2 \
    && cp node_modules/@farmfe/core-linux-x64-gnu/farm.linux-x64-gnu.node /workspace/repo/packages/core/binding/ \
    && npm install @farmfe/plugin-react-linux-x64-gnu@2.0.0-beta.0 \
    && cp node_modules/@farmfe/plugin-react-linux-x64-gnu/index.farm /workspace/repo/rust-plugins/react/npm/linux-x64-gnu/ \
    && npm install @farmfe/plugin-replace-dirname-linux-x64-gnu@2.0.0-beta.0 \
    && cp node_modules/@farmfe/plugin-replace-dirname-linux-x64-gnu/index.farm /workspace/repo/rust-plugins/replace-dirname/npm/linux-x64-gnu/ \
    && cd /tmp && rm -rf farm-bins

RUN npx tsc -b packages/core/tsconfig.build.json

RUN pnpm --filter @farmfe/cli run build

RUN node node_modules/playwright-chromium/install.js

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
