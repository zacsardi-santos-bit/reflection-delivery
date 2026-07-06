FROM node:18

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/cypress-io/cypress.git && \
    git fetch --depth 30 origin 380f4e7d6036fc4fbd8c1443c011cb8414b231a7 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/cli

RUN node -e "const fs=require('fs'); const p=JSON.parse(fs.readFileSync('package.json')); delete p.devDependencies['@packages/root']; fs.writeFileSync('package.json', JSON.stringify(p,null,2))"

RUN npm install --ignore-scripts --legacy-peer-deps 2>&1 | tail -20

RUN npm install --ignore-scripts --legacy-peer-deps --no-save mocha-banner@1.1.2 2>&1 | tail -10


# OFFLINE-BAKE: pre-fetch (into cache + node_modules) the EXACT deps the golden/test
# patch adds to package.json -- absent at base commit, so the test-time re-install would
# otherwise need the network. We add them in the repo (correct resolution) then restore
# the base manifest from git so the repo stays at base_commit (NOT applying the patch).
RUN DEPS="@octokit/auth-app@3.6.1 @octokit/core@3.6.0"; cd /workspace/repo && \
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
