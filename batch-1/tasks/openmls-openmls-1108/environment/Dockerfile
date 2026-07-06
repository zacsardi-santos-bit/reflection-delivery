FROM rust:1.85

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN cargo install cargo-nextest --version 0.9.100 --locked

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/openmls/openmls.git && \
    git fetch --depth 30 origin 9f80174ce1957c3470675dd87654577e5bb42509 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/openmls

RUN sed -i 's|tls_codec = { git = "https://github.com/RustCrypto/formats.git".*|tls_codec = { git = "https://github.com/RustCrypto/formats.git", rev = "f1f48f1a71caf13e240d632407141c8bbe3d07b9", features = ["derive", "serde_serialize", "mls"] }|' Cargo.toml && \
    sed -i '/x64 targets get evercrypt/d' Cargo.toml && \
    sed -i "/^\[target\.'cfg(target_arch = .x86_64.)/,/^features.*evercrypt/d" Cargo.toml && \
    sed -i '/openmls_evercrypt/d' Cargo.toml && \
    sed -i 's/evercrypt = \["openmls_evercrypt"\]/evercrypt = []/' Cargo.toml && \
    cd /workspace/repo && \
    git config user.email "build@docker" && \
    git config user.name "Docker Build" && \
    git add openmls/Cargo.toml && \
    git commit -m "Remove yanked evercrypt dependency"

RUN cd /workspace/repo/openmls && cargo fetch

RUN cd /workspace/repo/openmls && cargo build --tests 2>&1 | tail -20

WORKDIR /workspace

# >>> final-git-hygiene (compliance) >>>
RUN cd /workspace/repo 2>/dev/null && ( \
    B=9f80174ce1957c3470675dd87654577e5bb42509; H=$(git rev-parse HEAD 2>/dev/null); \
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
