FROM rust:1.85

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential libssl-dev pkg-config \
    && rm -rf /var/lib/apt/lists/*

RUN cargo install cargo-nextest --version 0.9.100 --locked 2>&1 | tail -5

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/SeaQL/sea-orm.git && \
    git fetch --depth 30 origin e38416ae0a3cc0453c36cbe8a9357a99d5f621c2 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN sed -i 's/sea-query-binder = { version = "0\.8\.0-rc\.1"/sea-query-binder = { version = "=0.8.0-rc.1"/' Cargo.toml && \
    sed -i 's/sea-query = { version = "1\.0\.0-rc\.2"/sea-query = { version = "=1.0.0-rc.3"/' Cargo.toml && \
    sed -i '/^\[patch\.crates-io\]/,$ d' Cargo.toml

RUN cargo fetch 2>&1 | tail -10

RUN cargo build --test partial_model_tests --features default,sqlx-sqlite,runtime-tokio-native-tls 2>&1 | tail -30 || true

ENV DATABASE_URL="sqlite::memory:"

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
