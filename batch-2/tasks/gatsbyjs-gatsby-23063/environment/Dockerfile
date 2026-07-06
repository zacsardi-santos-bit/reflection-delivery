FROM node:12-bullseye

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/gatsbyjs/gatsby.git && \
    git fetch --depth 1 origin 2a2802be8ee8c752d8f81ae51c94543fe8fbd7d7 && \
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

RUN sed -i 's|https://nexus.stackline.com/repository/npm-group/|https://registry.npmjs.org/|g' yarn.lock

RUN yarn install --frozen-lockfile --ignore-engines 2>&1 | tail -20

RUN node -e "const fs=require('fs'); const files=require('child_process').execSync('find /workspace/repo/node_modules -name \"package.json\" -path \"*/@babel/compat-data/package.json\"').toString().trim().split('\\n'); files.forEach(f=>{try{const p=JSON.parse(fs.readFileSync(f,'utf8')); if(p.exports){delete p.exports; fs.writeFileSync(f,JSON.stringify(p,null,2)); console.log('patched',f);}}catch(e){}});"


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
