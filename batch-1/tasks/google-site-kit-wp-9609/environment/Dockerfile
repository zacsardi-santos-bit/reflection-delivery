FROM node:18

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN printf '#!/bin/bash\nif [ "$1" = "apply" ]; then\n  /usr/bin/git apply --reject "${@:2}" 2>/dev/null\n  exit 0\nfi\nexec /usr/bin/git "$@"\n' > /usr/local/bin/git && chmod +x /usr/local/bin/git

WORKDIR /workspace

RUN /usr/bin/git init /workspace/repo && cd /workspace/repo && \
    /usr/bin/git remote add origin https://github.com/google/site-kit-wp.git && \
    /usr/bin/git fetch --depth 30 origin 5ee63965dddd0e1c70e033c431ddaddefe078753 && \
    /usr/bin/git checkout --detach FETCH_HEAD && \
    /usr/bin/git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    /usr/bin/git remote remove origin && \
    /usr/bin/git tag -l | xargs -r /usr/bin/git tag -d && \
    /usr/bin/git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 /usr/bin/git update-ref -d && \
    /usr/bin/git reflog expire --expire=now --all && \
    /usr/bin/git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN npm ci 2>&1 | tail -20

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
