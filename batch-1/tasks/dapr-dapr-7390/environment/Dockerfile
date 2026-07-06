FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN cp /usr/bin/git /usr/local/bin/git.orig

RUN printf '#!/bin/sh\nif [ "$1" = "apply" ]; then\n  shift\n  exec /usr/local/bin/git.orig apply --exclude="*.jpg" --exclude="*.jpeg" --exclude="*.png" "$@"\nfi\nexec /usr/local/bin/git.orig "$@"\n' > /usr/bin/git && chmod +x /usr/bin/git

WORKDIR /workspace

RUN git.orig init /workspace/repo && cd /workspace/repo && \
    git.orig remote add origin https://github.com/dapr/dapr.git && \
    git.orig fetch --depth 30 origin 43d9f5ab5fca85f4ad876e680266daf0f33d7f06 && \
    git.orig checkout --detach FETCH_HEAD && \
    git.orig submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git.orig remote remove origin && \
    git.orig tag -l | xargs -r git.orig tag -d && \
    git.orig for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git.orig update-ref -d && \
    git.orig reflog expire --expire=now --all && \
    git.orig gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download 2>&1 | tail -5 || true

RUN go build -tags=unit,subtlecrypto ./pkg/api/universal/... 2>&1 | tail -20 || true

RUN go build -tags=unit ./pkg/runtime/processor/secret/... 2>&1 | tail -20 || true

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
