FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates gnupg \
    xvfb \
    libgtk-3-0t64 libxss1 libnss3 \
    libx11-xcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 \
    libxi6 libxtst6 libxrandr2 libgbm1 libdrm2 \
    libpangocairo-1.0-0 libatk1.0-0 libcairo2 libgdk-pixbuf2.0-0 \
    libasound2t64 \
    fonts-liberation \
    && rm -rf /var/lib/apt/lists/*

RUN install -dm 755 /etc/apt/keyrings \
    && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_18.x nodistro main" > /etc/apt/sources.list.d/nodesource.list \
    && apt-get update && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/electron/electron.git && \
    git fetch --depth 30 origin 2e5cb930de80e79000887ba02372960fe1efbb20 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN npm install electron@3.1.13 2>&1 | tail -10

RUN cd spec && npm install 2>&1 | tail -20

RUN npm install yargs@6 2>&1 | tail -5

RUN mkdir -p node_modules/electabul \
    && printf "'use strict'\nmodule.exports = { Coverage: function(o){ return { setup: function(){}, addCoverage: function(){}, generateReport: function(){} }; } }\n" > node_modules/electabul/index.js

RUN npm install -g asar 2>&1 | tail -5

RUN ASAR_PATH=/workspace/repo/node_modules/electron/dist/resources/electron.asar \
    && EXTRACT_DIR=/tmp/electron-asar \
    && asar extract "$ASAR_PATH" "$EXTRACT_DIR" \
    && printf "require('/workspace/repo/lib/browser/guest-window-manager.js')\n" > "$EXTRACT_DIR/browser/guest-window-manager.js" \
    && asar pack "$EXTRACT_DIR" "$ASAR_PATH" \
    && rm -rf "$EXTRACT_DIR"

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
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
