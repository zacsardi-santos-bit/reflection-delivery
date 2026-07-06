FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    postgresql postgresql-contrib \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g pnpm

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/supabase/supabase.git && \
    git fetch --depth 1 origin 0eaf282070285fc8acfb5d5aef7d70ea96684d98 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

RUN rm /workspace/repo/pnpm-workspace.yaml && \
    cd /workspace/repo/packages/pg-meta && \
    pnpm install 2>&1 | tail -20

RUN PGVERSION=$(ls /usr/lib/postgresql/ | head -1) \
    && mkdir -p /var/lib/postgresql/testdata /var/run/postgresql /var/log/postgresql \
    && chown postgres:postgres /var/lib/postgresql/testdata /var/run/postgresql /var/log/postgresql \
    && su postgres -c "/usr/lib/postgresql/${PGVERSION}/bin/initdb -D /var/lib/postgresql/testdata --auth=trust" \
    && su postgres -c "/usr/lib/postgresql/${PGVERSION}/bin/pg_ctl start -D /var/lib/postgresql/testdata -l /var/log/postgresql/setup.log -w" \
    && su postgres -c "psql -f /workspace/repo/packages/pg-meta/test/db/00-init.sql" \
    && su postgres -c "psql -f /workspace/repo/packages/pg-meta/test/db/01-memes.sql" \
    && su postgres -c "/usr/lib/postgresql/${PGVERSION}/bin/pg_ctl stop -D /var/lib/postgresql/testdata -m fast"

ENV DATABASE_URL=postgresql://postgres:postgres@localhost:5432

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# >>> offline-node-bake (new4261) >>>
# Bake dep caches/stores + node_modules at build (network on) so the embedded
# test-time install resolves fully OFFLINE. Caches pinned to HOME-independent paths.
ENV npm_config_cache=/root/.npm
ENV YARN_CACHE_FOLDER=/root/.cache/yarn
ENV COREPACK_ENABLE_STRICT=0
ENV CI=true
# [sanitized] removed patch COPY (no solution/test in image).patch
# Force OFFLINE at TEST time so the embedded install can't reach the network.
RUN printf 'offline=true\nprefer-offline=true\naudit=false\nfund=false\nupdate-notifier=false\n' > /root/.npmrc; \
    printf 'yarn-offline-mirror "/root/.cache/yarn"\nyarn-offline-mirror-pruning false\n' > /root/.yarnrc; \
    ( command -v pnpm >/dev/null 2>&1 && pnpm config set store-dir /root/.local/share/pnpm/store --global 2>/dev/null && pnpm config set offline true --global 2>/dev/null && pnpm config set prefer-offline true --global 2>/dev/null ) || true
ENV YARN_ENABLE_NETWORK=false
ENV npm_config_offline=true
ENV npm_config_prefer_offline=true
ENV npm_config_audit=false
ENV npm_config_fund=false
# <<< offline-node-bake (new4261) <<<

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh

# --- offline-harbor: shrink .git below 100MB by dropping blob objects (keep commit+tree so HEAD==base and patches/tests still work on the on-disk working tree); expire reflog, drop tags/remotes; remove leaked patches ---
RUN cd /workspace/repo && \
    git cat-file --batch-all-objects --batch-check 2>/dev/null | awk '$2!="blob"{print $1}' | git pack-objects .git/objects/pack/pack 2>/dev/null && \
    ls .git/objects/pack/ && \
    NEWPACK=$(ls -t .git/objects/pack/pack-*.pack | head -1) && \
    find .git/objects/pack -name 'pack-*.pack' ! -name "$(basename "$NEWPACK")" -delete && \
    find .git/objects/pack -name 'pack-*.idx' ! -name "$(basename "${NEWPACK%.pack}.idx")" -delete && \
    find .git/objects -type d -regextype posix-extended -regex '.*/[0-9a-f]{2}' -exec rm -rf {} + 2>/dev/null; \
    git remote remove origin 2>/dev/null || true; \
    git tag -l | xargs -r git tag -d 2>/dev/null || true; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    rm -rf /workspace/repo/.git/logs; \
    du -sm /workspace/repo/.git | cut -f1
