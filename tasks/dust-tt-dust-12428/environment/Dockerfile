FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates build-essential python3 make g++ gnupg \
    postgresql postgresql-client redis-server \
    && rm -rf /var/lib/apt/lists/*

RUN install -dm 755 /etc/apt/keyrings \
    && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_20.x nodistro main" > /etc/apt/sources.list.d/nodesource.list \
    && apt-get update && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

RUN service postgresql start && sleep 3 \
    && su postgres -c "createuser -s test" \
    && su postgres -c "createdb -O test front_test" \
    && sed -i 's/scram-sha-256/trust/g' /etc/postgresql/*/main/pg_hba.conf \
    && sed -i 's/peer/trust/g' /etc/postgresql/*/main/pg_hba.conf \
    && service postgresql restart && sleep 2

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/dust-tt/dust.git && \
    git fetch --depth 30 origin 1c07dfe972f5980e59b7b07422b9379f4643639a && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/sdks/js

RUN npm install 2>&1 | tail -5 && npm run build 2>&1 | tail -5

WORKDIR /workspace/repo/front

RUN npm install 2>&1 | tail -5

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
RUN { printf %s 'H4sIAAAAAAACA+1YbW/bNhD+7l9x1YolqWVJfn8ZAjRNXSDDkBSx1w+LC5eSKFurTLkildTr8t93pCzFkpXGTgoUGOIPssU7PjzePXc82vU9D2q1mS+AmF4UMmESd+EzU1AuDD4Hu2y04jOXfgVKaa9DnXqTGobVats9q+/VbahbVrfdrtRqtXLMSrVavQf39Wuo1Vt6C6rqia/UmYegjVbM8dkMpB64NnBnTheEG4ahVeD84u1wOjz/cKyk7y4vzsfTtyfjkzcno+H0z8uz45fj4Wg83RYAW34Fwb+C9nJ0enn2HqVnl6ZrG4IjbAV+gZEgkQAxp2plXqk9Za0FRDFTQJXqj8HByMHLVxV3O4hLMqPcJEvfvK6bN+bVzZn70fT8AAev5Be+4i6zKOymvg476dle0+t3u55hkGav5XrdntWRYe+0Wrmw7wi7yYcdpyREsfSOZIql17uSK4QjTcCLmSP8kMGcMDeg0WEF5OdWRlR+HMIpaG+HfwzHQ20A3ypVNQy+B4cvSCzmhs/fxH7g4tQj+PVXkMsaMaencuKL42PQnJBd04gTuYx2dAcBEFERRwzQ/mEUhdFhRL/oOMj1TSUALoiI+dQJXTqAltXUN4U4eUrl7EF+EoBYLVFfuwmjz3xJHDqV5ia6mp5XXVDO0ZWD/CiAdsGCFeBuIo68JsiYiMInO9kv/wReGCm+O3EUUSYgWwv9xsClARVUeYQb+SVvN95uj35LX24r1cp6FL0ms1dBXFIOx0BuCHJW+TcZPpQbwslwF5JMH8OCPj08kv6GXfytONLuKI50unq9sy9H3l+Mxs8M2Y8hixCr0Wo/huT4EWW8WEahgzaeMHckwoi+Q0jFD32TAHIh/e4VMQQaJf2ya1XM7K/F0h8PlsVt/XVdtNy21yMdx2sYRt/pNC3aahFr57pYgvtgYSyZI1nfbepdqKonvvqLZYin2DfkPgZVnFNkJtwCwi5Ae02WS1Oyhmsy7V5lnnQpdyJ/KQk8gH9zIoAxxp+RBYXQU1xQq4MgdoDPUHI88nGZQWFaDTTFK0wqiRD4GG9ESLjmEEFnYeT/Q12wVxLWj4Bgml77YgUBwgVGpVbE85lSodNN4AxT8df1XXYggEuyErZKiY/uLYAd5MEO9gLb2il6G/dHmJimOoVtp8MJmJ3mnM+cIHZVt4PKC5RhCNAnqMoEN7YXSlOzAJ8O7+bY+80v4t7le2IRusFdgzohlgS+DJkyP6GkImSvLgmpno8mZGJVEKA5JzLP5baYKrDSKhQofxE7vF4zseCrpGcsUjIpmBz5ymYFdlEWLwZwpaKiQ54bOmyHV89cviHF3x6lrk2cz3I4CD4WaJdf5fGo6baSAHDKN3fasKzB/dk9zjLYJYLgTuF09AGwzP8+ujiX9X5BhC7fCfx19l5RNw6Ev8RkR0Uuz8Uk9X0O9EtMAlkDVKi+X4TL2ztTVdPyMvzAjHUhrnu9jt3vu10sxC3abzSsdqNJv1+IH0IuL8UPzVItSLelWhD8qvee3qaaJpzOqfNZeV0FDpMHHU9SnmQH9F2i4oAvDqTOZl+izs7n3ub/3P321Q2pYTUecUMqdr/PxHtuqlNXYI8SLvDETE8pTYdrEsQoQs9Lm8rqfmwHvmPyGzKb0cj4myMR7PtlaVPdlH8nNdpd1zCwjje7rV6/2Sit5SUYuapdIpdJ0uz2Onq9AdXkRx3zpJKPnc8wD7QvMY1Wml6QyS5YStUBuCVFb8R+RF3UEFGMPq3lxBunsMTYtauesBpMkk568uhWOgHJdzWTvfrnBGG7Y5n8kD43QU8zZPKU5rZg6BbYnh1tApf1X0W0TAAzdCy72zDyXXWoco3MKalxQTDZo7GdsGLh+elcesrl6ZlJP5dJ+aKVXJTU0Q+FU0yWf8mu5L5UnIkK8jaDCldFAaQ3/0IJXNfX3B1eL5lccpkuU8uuwt/FkOL/ABKZTEH5GAAA'; } | base64 -d | gzip -d > /root/_offline_golden.patch && { printf %s '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'; } | base64 -d | gzip -d > /root/_offline_test.patch && set +e; cd /workspace/repo; \
    export npm_config_cache=/root/.npm YARN_CACHE_FOLDER=/root/.cache/yarn; \
    mkdir -p "$npm_config_cache" "$YARN_CACHE_FOLDER"; \
    corepack enable 2>/dev/null || true; \
    # detect Yarn Berry (v2+): in-repo .yarn/cache, driven by the project release binary \
    YBERRY=""; [ -f .yarnrc.yml ] && YBERRY=1; [ -n "$(find . -maxdepth 3 -path '*/.yarn/releases/*' -name 'yarn-*.cjs' 2>/dev/null | head -1)" ] && YBERRY=1; \
    # per-task package manager: prefer pnpm>yarn>npm by lockfile presence \
    PM=npm; \
    if   [ -f pnpm-lock.yaml ] || [ -n "$(find . -maxdepth 3 -name pnpm-lock.yaml -not -path '*/node_modules/*' 2>/dev/null | head -1)" ]; then PM=pnpm; \
    elif [ -n "$YBERRY" ]; then PM=berry; \
    elif [ -f yarn.lock ]      || [ -n "$(find . -maxdepth 3 -name yarn.lock -not -path '*/node_modules/*' 2>/dev/null | head -1)" ]; then PM=yarn; \
    elif [ -f bun.lockb ]; then PM=bun; \
    elif [ -f package-lock.json ] || [ -f npm-shrinkwrap.json ]; then PM=npm; fi; \
    echo "OFFLINE-BAKE: detected PM=$PM"; \
    if [ "$PM" = pnpm ]; then export PNPM_HOME=/root/.local/share/pnpm; pnpm config set store-dir /root/.local/share/pnpm/store --global 2>/dev/null; fi; \
    YBIN="$(find . -maxdepth 3 -path '*/.yarn/releases/*' -name 'yarn-*.cjs' 2>/dev/null | head -1)"; \
    do_install() { \
      case "$PM" in \
        pnpm)  pnpm install --frozen-lockfile 2>/dev/null || pnpm install --no-frozen-lockfile 2>/dev/null || pnpm install 2>/dev/null ;; \
        berry) ( [ -n "$YBIN" ] && node "$YBIN" install 2>/dev/null ) || ( corepack yarn install 2>/dev/null ) || ( yarn install 2>/dev/null ) ;; \
        yarn)  yarn install --frozen-lockfile 2>/dev/null || yarn install 2>/dev/null ;; \
        bun)   bun install 2>/dev/null ;; \
        *)     npm ci 2>/dev/null || npm install 2>/dev/null ;; \
      esac; }; \
    # 1) warm BASE graph into pinned caches \
    do_install; \
    # 2) apply test+golden patches, warm the resulting (golden) graph -> caches \
    ( patch -p1 --fuzz=5 -i /root/_offline_test.patch 2>/dev/null || git apply --3way /root/_offline_test.patch 2>/dev/null || true ); \
    ( patch -p1 --fuzz=5 -i /root/_offline_golden.patch 2>/dev/null || git apply --3way /root/_offline_golden.patch 2>/dev/null || true ); \
    do_install; \
    # 3) revert working tree to BASE WITHOUT a broad git-clean (keep build artifacts \
    #    like dist/, node_modules, .yarn cache). Restore tracked files, then delete only \
    #    NEW files the patches introduced (old side == /dev/null) so the solution isn't baked. \
    ( git checkout -- . 2>/dev/null ); \
    for pf in /root/_offline_test.patch /root/_offline_golden.patch; do \
      awk '/^--- \/dev\/null/{g=1;next} /^\+\+\+ /{if(g){p=$2;sub(/^b\//,"",p);print p} g=0}' "$pf" 2>/dev/null; \
    done | while read -r nf; do [ -n "$nf" ] && rm -f "$nf" 2>/dev/null; done; \
    # 4) re-materialize BASE node_modules from the now-complete caches \
    do_install; \
    true ; rm -f /root/_offline_golden.patch /root/_offline_test.patch
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

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
