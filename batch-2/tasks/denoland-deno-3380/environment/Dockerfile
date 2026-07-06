FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch unzip \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://github.com/denoland/deno/releases/download/v0.25.0/deno_linux_x64.gz | gunzip -c > /usr/local/bin/deno \
    && chmod +x /usr/local/bin/deno

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/denoland/deno.git && \
    git fetch --depth 1 origin e6fdb2628fdeeae0c7b06c25214b2edba96364dd && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

ENV DENO_DIR=/tmp/deno_cache

RUN mkdir -p /tmp/deno_cache && \
    cd /workspace/repo/std && \
    NO_COLOR=1 deno -A ./testing/runner.ts --allow-none node/require_test.ts || true

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
# --- offline toolchain fix: ensure the language toolchain (cargo/rust) is installed on the login PATH ---
RUN command -v cargo >/dev/null 2>&1 \
    || (apt-get update && apt-get install -y --no-install-recommends cargo && rm -rf /var/lib/apt/lists/*) \
    || (apk add --no-cache cargo) || (dnf install -y cargo) || (yum install -y cargo) || true

RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
