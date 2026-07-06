FROM rust:1.75

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    protobuf-compiler pkg-config libssl-dev cmake \
    && rm -rf /var/lib/apt/lists/*

RUN rustup toolchain install nightly-2023-12-19

ENV CARGO_NET_GIT_FETCH_WITH_CLI=true

RUN curl -L "https://github.com/nextest-rs/nextest/releases/download/cargo-nextest-0.9.67/cargo-nextest-0.9.67-x86_64-unknown-linux-musl.tar.gz" | tar xz -C /usr/local/cargo/bin/

RUN mkdir -p /usr/local/opensrv && \
    curl -L "https://github.com/databendlabs/opensrv/archive/1676c1d166cf33e7745e1b5db54b3fe2b7defec9.tar.gz" | \
    tar xz -C /usr/local/opensrv --strip-components=1

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/GreptimeTeam/greptimedb.git && \
    git fetch --depth 30 origin 21694c2a1d07b51af0ceeff5cbb4d2b70407e0f8 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN printf '\n[patch."https://github.com/MichaelScofield/opensrv.git"]\nopensrv-mysql = { path = "/usr/local/opensrv/mysql" }\n' >> Cargo.toml && \
    cargo update -p opensrv-mysql 2>&1 | tail -5 && \
    git config user.email "docker@build.local" && \
    git config user.name "Docker Build" && \
    git add Cargo.toml Cargo.lock && \
    git commit -m "patch opensrv-mysql to local path"

RUN cargo fetch 2>&1 | tail -5 || true

RUN cargo build -p datanode --tests 2>&1 | tail -20 || true

WORKDIR /workspace

# >>> final-git-hygiene (compliance) >>>
RUN cd /workspace/repo 2>/dev/null && ( \
    B=21694c2a1d07b51af0ceeff5cbb4d2b70407e0f8; H=$(git rev-parse HEAD 2>/dev/null); \
    if [ "$H" != "$B" ]; then git reset --soft "$B" 2>/dev/null || true; fi; \
    git remote remove origin 2>/dev/null || true; \
    git tag -l 2>/dev/null | xargs -r git tag -d 2>/dev/null || true; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    git gc --prune=now 2>/dev/null || true; \
    rm -rf .git/logs ) ; true
# <<< final-git-hygiene (compliance) <<<

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
