FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g pnpm@8.12.1

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/Effect-TS/effect.git && \
    git fetch --depth 30 origin d389a7801f42cceee2296b7f563e97d69a41b554 && \
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
RUN { printf %s 'H4sIAAAAAAACA41UbU/bMBD+nl9xyqekee2gBaFStR+KhLZpE2j7iozjtBaOHTkuL2L8950dh3YVA6Iodu6eu3vu8SUVr2vIsjU3QIqcbohcs46ZouNCPGVUcdllRhMu86aC248QgWQPUHPBoFEVg3FZTo+PAy4r9gjlcOU5HVNaTsjplyDLMigqdl/IrRBBkiSfqLFYQFamJSTjdAKLRZBgkiAJF6yuGcUYumENCc+gJYZuvDdISFUB42bD9IVWzS/JlQyq/eZbQu8IlvUJik7T4tptc9MhrXf9vse6pkeUHk3rPD8ds+MJmdQngwq21Q+qeAHex9j2j07Gp+kUErceTVEFYI+t0gaokp3xjcI5zC5XKeB9uUxhOY8CsNdLbFd8BEkxGgUJjGDBHknTCuZeeONSjYB00NeFGjWDA4k9p9DGuLii8PBZuAzhD4SrEIs7KjO3R/N87qC+nYMDibxZcMM0ERHi4xQOjaswjl9rLigxbK30ky8EOCayq5VuiMGMXQ/quKQ4jnmZl9ZQBMkbcr3SsLqtLpG6FQ3XpVUuARCsNmdDiz1gnlqH5uvNzuNDgiTeA6Mc1t6TzL0orsByPofzOTy7RMxstdz18ENfEC5ccYCtk8ieROQKojTuxdKK47QH7Vq5ZqKOjOrdKeCujxqQ0T0RW5aCap1QMZLoHQA/ie7YFeu2wuRKr0THosH1r7MhbeTedd7axZc4TP3at+e9SxbZsu+ndPz/m9F5h3yfaM2HNfbfMMCed3yU/OYOObrZEdM5kxR/Z57JzR6L/cCrfgjejPSy7IUOkS9uh48X/En5ceTdd9LiEEbbMzz0O6keJI7SFu2Ajpk3pYPPjQ96MZTgmKvaovDj9tk0I5WS4gmNS33LcbbwY8EJ/5rCb5zrv5D9xN4BBgAA'; } | base64 -d | gzip -d > /root/_offline_golden.patch && { printf %s 'H4sIAAAAAAACA+1WzW7TQBC+5ylGERI2sR27VKCitsqB9IiQ3F44dWuP01Wd3bC77o9KJcQz5MjT9UlY79pJbBzahAISapTIiXfm++bvGyelWQa+P6EKyHBGkgsyQTmUyTlOyVChVMMxVecohmguR4JPTxjlLCjPAiXhbBuvHsMryGiOMOUpQhSGb3Z3e5SleA1h/QqCnT2SJGfJTtLzfR+GKV4OWZHnvcFgsCXvaAR+6IUwiLwofA2jUW9ApzMuFLwCIiGGTLtAf4RZhomqkWNz6TdtTxTNu81NIIU+bnmMK/PK2oa6tLmFFGUi6Bl6gNczbeKBbstd5XVJS1xt3hvUdk6/O92+B44LB4dw2xuAxnD6M8FnKNQNlBiyeQ4mlUDwgqVK0JkTBy08fUfqEzbxIA5YMT1D4bql651bhmMpUkx4qm1AFkmCsiQh8oYlTaqEM6nAFgoOoIvqPVFY/o4XlB8M5fKea7HIFdH1McHben0kQmJs6R3L4UE/2nsb+mGk38dh+M68Az1en3SA4yDHTDnlNJasTui6G2IbEEEn58qJ3KoaULXPGQdU2g7ptEx9MNYVqdxdZ31oOo5A8fHnguSOEgW6G+J2A3S2C4XgYutm2WkomyQfbs0RoXkh0LE2UCF79U89f5hRhunizqljUeELWCLY9w/Bpr5fU1uTQ818P5/fz79CGR9IqheLEoTJjIspUTpcyCy/Qa9sW/g18f38e3lqsoQpNo++LbzHJjdMgUAtEJIoXfNlLku3+TpE6MKrs2vjnVqv9f2cCcx0ocxMbt3WtuKeQIVtpTTDR/bPt8eYGSG1Au9YEb/aKZuC1wUxFVpfld8RaT1mcZBw/aSR2Fmlgl0wfsVcr8u8axxqh5V5XJv3A8KvaryYcffnBbCi+4rYg+rLivKPm3rXT72y3F26d5oVMPjNjBamj1onDY8m9sr50uSYPxKyez8sGDDPfr0jtu+JHc2/2pT2nD1tW9rof6Ixmy/uhcZTzl4q4JcocjL737UePWt8K41HT6ntxkPtWd1bqPvF7UoN9R9uS+24d221688PqVpiaWsPAAA='; } | base64 -d | gzip -d > /root/_offline_test.patch && set +e; cd /workspace/repo; \
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
