FROM rust:1.75

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libncursesw5-dev pkg-config clang libclang-dev \
    && rm -rf /var/lib/apt/lists/*

ENV LIBCLANG_PATH=/usr/lib/llvm-14/lib

RUN curl -LsSf https://get.nexte.st/latest/linux | tar zxf - -C /usr/local/bin

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/mimblewimble/grin.git && \
    git fetch --depth 30 origin b8c508d2555a631c144a13ff969a7cf90a052179 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cargo vendor /workspace/vendor

RUN ZD=/workspace/vendor/zeroize_derive/src/lib.rs && \
    sed -i 's#"( drop )"#"( drop )" | "(drop)"#g' "$ZD" && \
    sed -i 's#"( no_drop )"#"( no_drop )" | "(no_drop)"#g' "$ZD"

RUN python3 -c "import json,hashlib; p='/workspace/vendor/zeroize_derive/src/lib.rs'; cf='/workspace/vendor/zeroize_derive/.cargo-checksum.json'; cs=json.load(open(cf)); cs['files']['src/lib.rs']=hashlib.sha256(open(p,'rb').read()).hexdigest(); json.dump(cs,open(cf,'w'))"

RUN mkdir -p /workspace/.cargo && \
    printf '[source.crates-io]\nreplace-with = "vendored-sources"\n\n[source.vendored-sources]\ndirectory = "/workspace/vendor"\n' > /workspace/.cargo/config.toml

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
