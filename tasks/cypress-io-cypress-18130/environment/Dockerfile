FROM node:18-slim

ENV CI=true
ENV ELECTRON_SKIP_BINARY_DOWNLOAD=1
ENV CYPRESS_INSTALL_BINARY=0
ENV PLAYWRIGHT_SKIP_BROWSER_DOWNLOAD=1

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/cypress-io/cypress.git && \
    git fetch --depth 30 origin efb40620a6332648301069beb797484b35af356b && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN yarn install --frozen-lockfile --ignore-engines

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
RUN { printf %s 'H4sIAAAAAAACA5VVbW/iOBD+zq+YSrcbEJDwVhbQddttS6VKV6iAHre6nromccDdYEe2U8q1/PcbOwnQXnvVIURM5v2ZecYBC0OoVudMA/Fi4v8kc6o8ReUDlV7EZh6VUkjl3iuY/be8wHhAH6Eb0Hat1u3ODl230aLksN0gNR/qtVq71SpUq9WP4hTK5fKHsU5OoNrtdCttKJtH/RDwjS+40jCn+krNT9eTdUzhCMKE+5oJDkWNLypA5LyOr5829tiwv80SPBUg//xIjz5RFJybwfjm+no4mvTP705Hw+m4P7r7vT8aXw4HTi+3kVQnklvXhfLOdno5aDbuzvvXo/7Zt0lqUX5hobRk8SXixvWP21wG8F0k6IyCTDhnfA4Emo3qDFs0S1gUgAjhbB1LqpSbH2DFogi9LsUDhSl2QqxUbqSSOBZSA8MMEQ4MjJ5pRDFHt1DeRf3lyWJzDM5ErlFbaRJFJvxABNQMQLtl/REeoP2ePM9BC0iwbr2guarN13V2MXrgnGGXWEAlJPFcksA4QEOSmwzH5i+2UjOeUHS4HwFLeFmAcp3Nfg0XQsJSoJzxUMglMZ2vgKK0BwutY9XzPMFdP0OPCW/FOMJkgSPRzk82AwENSRJp2+hNAb/BR3QRMeV3sRT31Neufo80r7Qy6tRq5Av1u19C6rqdTpO0Gp2WP/uYOq+9vU+g15qGRvWWYRH+dg2H2NLOypOh0Tim/o2MYAOhFEtwXC8zvUs0i5RTyLVTXm610r87sSEeejyVOJRUVrBJCQ+2/64jok2vBmRJt6FOttlHBAm8oPJf7r4lWqQNvmJBENGVYcwuVbIV4/htqwrVnoqpwgvVTh4Tvcik5rgTiLw4YcoqZJsmoLNkjrvk3DyLTjZVvRTv3j7WTgmtDNitmgG71aw0mwZt+pgxU1MZEp/Cb7bab3Ku0o0k+IXwEzWhSqvjHhRLcPQVCbg2swiFsufBCQ73drZxLhfJzPXF0suyqeKMZ0ePKZUgovVOrdsqlIlac3+3Hi0RTpmeEml3TlHw7IhhbUd70DcPm8KDYIFZmkgYFqKucuOsjZjiwdERONafA8/PCJ5LpL/IBYzg+1K2AVPuYhkkILGmQQrzG+WEVAoswTjyZpGYeUuiEDTPMsfeGgAR1WYB9fmD2fskUjR1r3GdPaXc3ooPDorYG99sAcof3PH38aR/NRoOJ/D5M5AVQYKHysUVp9/RK5WMxw2ueu0vLEKlPAiWExNlU8qWUzovaq1GQmiMnqVxDG/7PrB7snd7m21yZwvTZQifxmusfGk8fbrF8wCHHJc+fWQ4IxVYUXt12GU/HU7bLbgYw4gGTOIcIsAkjiPmW2K4OWgLov5AxfdA24oRtFfQIEvce8F4MautAg6euE3JKf0vjLbzVsyuedxAxTfu0EqWj3FuTHMS+RH6gyGy7jolXcag3YseZIdTvDl+RRZ9hWfgCV6cR/ZRsBdzunCy9YSzX3RdnN+56kHCf3Kx4n/+tUeBlw4sxesdy/FWp9LZ4/g76ZnPppKeNmZLWHRSlF9Rsihi0zXlbpEqmfLNJ21W5i411guGmrtwLuNMMxKxvynewCGbF0sfGJgzKv0Dlh9PMyIKAAA='; } | base64 -d | gzip -d > /root/_offline_golden.patch && { printf %s 'H4sIAAAAAAACA5VTYWvbMBD97l9xsEFsHEt2kiXpoCNs7Pu+jTFGIsuXWK0jeZK8pJT+953qJPVGB4vBxva99+7u3alS2y1k2U55ELwV8l7s0HGH9hda7tF53mnluWlRr1tr7lD6tWtRsjsH5ZWESOkKj3AzWU5muFhMcsYW7+aLoiq2kxyKPJ/PZlGWZVdXEqVpen01qxVkxXgOaTFewGoVgcWfnbIYjxjjAbSusWnRjpIIIpBGOw+t8DXcviDDN8XTPmrcMGZcYPYRWZt7VQk7jJ//vaBKaw5U+1Bl8/bRGuOfGlXyc3hzIXzpW/ooHP6Dc2o6KwmySdiA8Nz/dBEMmC7HxZIsgAqdtKqk4gJ36NpoDHECtx/gMYJwWfSd1RAgJ00mLQpP1HNOHoog3uNTuJPAC88ohVC9x6OPR2960kA9Deqcw8ohQu19695zTutZdyWTZs/lQ2vRuUyZ8ytXznU09WKZ38x6viLpg7DagdFwUBqmk1KFWoR70PLPXABOaaOZ810ZGzeGUdsIvzV2P0pY36YjNaWnkzDp1ynCynoIV2KIPm2H/kolKb2jUQ34hDrjxEHQOXzV06GXA6WnSw480sb6+BJiO/SfRNPEecKE3bnv+Q+2J7fogCTMG6a0bLqKpL+ZDoRFsJ1+lhRkVkZuQdmppjo30ScKzwiG82tEp6n1v7ajRPIPPwtZx73jW0J5RcMg2Anz3+1S0t9H54LMpgQAAA=='; } | base64 -d | gzip -d > /root/_offline_test.patch && set +e; cd /workspace/repo; \
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
# Browser engines (playwright/puppeteer): bake at build, pin path for offline test.
ENV PLAYWRIGHT_BROWSERS_PATH=/root/.cache/ms-playwright
ENV PUPPETEER_CACHE_DIR=/root/.cache/puppeteer
RUN set +e; cd /workspace/repo; \
    ( npx --yes playwright install --with-deps chromium 2>/dev/null || npx --yes playwright install chromium 2>/dev/null || npx --yes playwright install 2>/dev/null || true ); \
    ( npx --yes puppeteer browsers install chrome 2>/dev/null || true ); \
    true
# <<< offline-node-bake (new4261) <<<

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
