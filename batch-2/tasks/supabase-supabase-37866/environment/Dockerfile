FROM node:22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g pnpm@9.15.5

RUN printf '#!/bin/bash\nif [ "$1" = "apply" ]; then\n  /usr/bin/git apply --reject "${@:2}" || true\nelse\n  exec /usr/bin/git "$@"\nfi\n' > /usr/local/bin/git && chmod +x /usr/local/bin/git

WORKDIR /workspace

RUN /usr/bin/git init /workspace/repo && cd /workspace/repo && \
    /usr/bin/git remote add origin https://github.com/supabase/supabase.git && \
    /usr/bin/git fetch --depth 1 origin e320368dc7bd3026f5b4e14762b3c8d6f4b61b19 && \
    /usr/bin/git checkout --detach FETCH_HEAD && \
    /usr/bin/git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    /usr/bin/git remote remove origin && \
    /usr/bin/git tag -l | xargs -r /usr/bin/git tag -d && \
    /usr/bin/git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 /usr/bin/git update-ref -d && \
    /usr/bin/git reflog expire --expire=now --all && \
    /usr/bin/git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN pnpm install --frozen-lockfile 2>&1 | tail -30

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

# offline-harbor: drop blob objects if .git>=90MB (keep commit+tree so HEAD stays valid; working tree stays on disk)
# NOTE: this image ships a /usr/local/bin/git wrapper; use /usr/bin/git explicitly to bypass it.
RUN bash -c 'cd /workspace/repo && \
  if [ "$(du -sm .git|cut -f1)" -ge 90 ]; then \
    /usr/bin/git rev-parse HEAD > /tmp/_H && \
    mkdir -p /tmp/keep && \
    /usr/bin/git cat-file --batch-all-objects --batch-check 2>/dev/null | awk "\$2!=\"blob\"{print \$1}" > /tmp/_nonblob && \
    /usr/bin/git pack-objects /tmp/keep/p < /tmp/_nonblob >/dev/null && \
    rm -rf .git/objects/pack/* .git/objects/[0-9a-f][0-9a-f] && \
    mv /tmp/keep/p-*.pack .git/objects/pack/ && mv /tmp/keep/p-*.idx .git/objects/pack/ && \
    /usr/bin/git reflog expire --expire=now --all 2>/dev/null; rm -rf .git/logs; \
    /usr/bin/git tag -l|xargs -r /usr/bin/git tag -d; /usr/bin/git remote remove origin 2>/dev/null||true; \
  fi'

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
