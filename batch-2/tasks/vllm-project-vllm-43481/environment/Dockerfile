FROM rust:1.95

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/vllm-project/vllm.git && \
    git fetch --depth 30 origin 0910f7e0e1f5ee9f2e5a6f76d0d09e68fddadc01 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/rust

RUN cargo build --tests -p vllm-chat 2>&1 | tail -10

RUN curl -LsSf https://get.nexte.st/latest/linux | tar zxf - -C /usr/local/bin

WORKDIR /workspace

RUN cd /workspace/repo && git ls-files --others --exclude-standard -z | tar --null -T - -czf /tmp/_pre_untracked.tgz 2>/dev/null || true
RUN (cd /workspace/repo && (git apply --ignore-whitespace --whitespace=nowarn /tmp/_test.patch 2>/dev/null || patch -p1 --fuzz=5 -i /tmp/_test.patch 2>/dev/null || true)); true \
    && ( cd /workspace/repo/rust && (cargo fetch --locked 2>&1 | tail -2 || cargo fetch 2>&1 | tail -2 || true); (cargo metadata --format-version=1 >/dev/null 2>&1 || true); (cargo test --no-run -p vllm-chat 2>&1 | tail -2 || cargo test --no-run 2>&1 | tail -2 || true); (cargo nextest run --no-run -p vllm-chat 2>&1 | tail -2 || cargo nextest run --no-run 2>&1 | tail -2 || true) ) ; \
    cd /workspace/repo && git checkout -- . 2>/dev/null; git clean -fdq 2>/dev/null; tar -xzf /tmp/_pre_untracked.tgz -C /workspace/repo 2>/dev/null; rm -f /tmp/_pre_untracked.tgz /tmp/_golden_toml.patch /tmp/_test.patch 2>/dev/null || true# --- offline-vllm-runtime-deps ---
ENV TIKTOKEN_RS_CACHE_DIR=/opt/tiktoken-cache
RUN mkdir -p /opt/tiktoken-cache && \
    (curl -fsSL -o "/opt/tiktoken-cache/$(printf %s 'https://openaipublic.blob.core.windows.net/encodings/o200k_base.tiktoken' | sha1sum | cut -d' ' -f1)" 'https://openaipublic.blob.core.windows.net/encodings/o200k_base.tiktoken' 2>/dev/null || true) && \
    ( cd /workspace/repo/rust && (cargo test -p vllm-chat 2>&1 | tail -2 || true); (cargo test -p vllm-server 2>&1 | tail -2 || true) ) ; true
ENV CARGO_NET_OFFLINE=true

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# OFFLINE-CARGO-BAKE
RUN cd /workspace/repo && (cargo fetch 2>/dev/null; cargo test --no-run 2>/dev/null; cargo nextest run --no-run 2>/dev/null; true)
ENV CARGO_NET_OFFLINE=true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
