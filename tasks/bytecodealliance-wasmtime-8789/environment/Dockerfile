FROM rust:1.78

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential pkg-config libssl-dev \
    && rm -rf /var/lib/apt/lists/*

RUN mv /usr/bin/git /usr/bin/git.real && \
    printf '#!/bin/sh\nif [ "$1" = "apply" ]; then\n  shift\n  /usr/bin/git.real apply --reject "$@" 2>&1 || true\nelse\n  /usr/bin/git.real "$@"\nfi\n' > /usr/bin/git && \
    chmod +x /usr/bin/git

RUN cargo install cargo-nextest --version 0.9.85 --locked

RUN rustup target add wasm32-unknown-unknown wasm32-wasi

WORKDIR /workspace

RUN /usr/bin/git.real init /workspace/repo && cd /workspace/repo && \
    /usr/bin/git.real remote add origin https://github.com/bytecodealliance/wasmtime.git && \
    /usr/bin/git.real fetch --depth 30 origin bdd7842263c1840c328d4ffc2a53323c83af5203 && \
    /usr/bin/git.real checkout --detach FETCH_HEAD && \
    /usr/bin/git.real submodule update --init --recursive --depth 1 2>/dev/null || true && \
    /usr/bin/git.real remote remove origin && \
    /usr/bin/git.real tag -l | xargs -r /usr/bin/git.real tag -d && \
    /usr/bin/git.real for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 /usr/bin/git.real update-ref -d && \
    /usr/bin/git.real reflog expire --expire=now --all && \
    /usr/bin/git.real gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cargo fetch --locked || cargo fetch

RUN cargo build --tests 2>&1 | tail -5 || true

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
