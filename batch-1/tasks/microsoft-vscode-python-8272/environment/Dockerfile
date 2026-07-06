FROM node:14-bullseye

RUN apt-get update && apt-get install -y git curl ca-certificates patch build-essential python3 libcairo2-dev libpango1.0-dev libjpeg-dev libgif-dev librsvg2-dev && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/microsoft/vscode-python.git && \
    git fetch --depth 30 origin 796f843d509e1ccec63c56981ee95f02fe77555c && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN npm install --legacy-peer-deps > /tmp/npm.log 2>&1 || (tail -50 /tmp/npm.log && exit 1)

RUN ./node_modules/.bin/tsc --skipLibCheck -p ./ > /tmp/tsc.log 2>&1 || (tail -80 /tmp/tsc.log && exit 1)

RUN printf "'use strict';\nconst Module = require('module');\nconst _orig = Module._load;\nModule._load = function(req, parent, isMain) {\n  if (req && req.includes('debugger/extension/adapter/logging')) {\n    try { return _orig.apply(this, arguments); }\n    catch(e) { if (e && e.code === 'MODULE_NOT_FOUND') { function DebugSessionLoggingFactory() {} return { DebugSessionLoggingFactory: DebugSessionLoggingFactory }; } throw e; }\n  }\n  return _orig.apply(this, arguments);\n};\n" > /workspace/logging-stub.js

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
