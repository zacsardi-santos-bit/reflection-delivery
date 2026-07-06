FROM node:18

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/web-infra-dev/rsbuild.git && \
    git fetch --depth 30 origin 032f413795adf9e6625bceea44ff6ee318d63115 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN corepack enable && corepack prepare pnpm@8.14.0 --activate


RUN { printf %s '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'; } | base64 -d | gzip -d > /tmp/golden.patch && git apply /tmp/golden.patch && pnpm install --frozen-lockfile && git checkout . && git clean -fd && rm -f /tmp/golden.patch

RUN NX_DAEMON=false pnpm run build

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
