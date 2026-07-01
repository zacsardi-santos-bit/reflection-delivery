FROM node:16-bullseye

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    libglib2.0-0 libnss3 libnspr4 libatk1.0-0 libatk-bridge2.0-0 \
    libcups2 libdrm2 libdbus-1-3 libatspi2.0-0 libxcomposite1 \
    libxdamage1 libxext6 libxfixes3 libxrandr2 libgbm1 libxcb1 \
    libxkbcommon0 libpango-1.0-0 libcairo2 libasound2 \
    libx11-6 libx11-xcb1 libxcursor1 libxi6 libxtst6 \
    libgtk-3-0 libgdk-pixbuf2.0-0 libxshmfence1 \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g pnpm@6

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/sveltejs/kit.git && \
    git fetch --depth 30 origin 3612bb066846f89a5aa3b3e2f764b33a8092a1ed && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN PLAYWRIGHT_SKIP_BROWSER_DOWNLOAD=1 pnpm install

RUN echo "aW1wb3J0IGdsb2IsIG9zCgpmaWxlcyA9ICgKICAgIGdsb2IuZ2xvYignL3dvcmtzcGFjZS9yZXBvL25vZGVfbW9kdWxlcy91dnUvZGlzdC9pbmRleC5tanMnKSArCiAgICBnbG9iLmdsb2IoJy93b3Jrc3BhY2UvcmVwby9ub2RlX21vZHVsZXMvLnBucG0vdXZ1Ki9ub2RlX21vZHVsZXMvdXZ1L2Rpc3QvaW5kZXgubWpzJykKKQpmb3IgZiBpbiBmaWxlczoKICAgIHJlYWwgPSBvcy5wYXRoLnJlYWxwYXRoKGYpCiAgICB3aXRoIG9wZW4ocmVhbCwgJ3InLCBlbmNvZGluZz0ndXRmLTgnKSBhcyBmaDoKICAgICAgICBjb250ZW50ID0gZmgucmVhZCgpCiAgICBjb250ZW50ID0gY29udGVudC5yZXBsYWNlKAogICAgICAgICd3cml0ZShQQVNTKTsnLAogICAgICAgICd3cml0ZShwcm9jZXNzLmVudi5VVlVfVkVSQk9TRSA/ICgiICDinJMgIiArIHRlc3QubmFtZSArICJcXG4iKSA6IFBBU1MpOycKICAgICkKICAgIGNvbnRlbnQgPSBjb250ZW50LnJlcGxhY2UoCiAgICAgICAgJ3dyaXRlKEZBSUwpOycsCiAgICAgICAgJ3dyaXRlKHByb2Nlc3MuZW52LlVWVV9WRVJCT1NFID8gKCIgIOKclyAiICsgdGVzdC5uYW1lICsgIlxcbiIpIDogRkFJTCk7JwogICAgKQogICAgd2l0aCBvcGVuKHJlYWwsICd3JywgZW5jb2Rpbmc9J3V0Zi04JykgYXMgZmg6CiAgICAgICAgZmgud3JpdGUoY29udGVudCkKICAgIHByaW50KCdQYXRjaGVkJywgcmVhbCkK" | base64 -d | python3

RUN cd packages/kit && npx rollup -c

RUN npx playwright install chromium

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
