FROM rust:1.75

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/biomejs/biome.git && \
    git fetch --depth 1 origin 200e662231ea8bfa7b52053b2d7032b780e73887 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN curl -LsSf https://get.nexte.st/latest/linux | tar zxf - -C ${CARGO_HOME:-~/.cargo}/bin

RUN cargo fetch

RUN cargo build -p biome_cli --tests 2>&1 | tail -5 || true

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# >>> offline-cargo-bake (new4261) >>>
# Warm $CARGO_HOME for the EXACT post-golden+test dependency graph, then revert
RUN set -u; cd /workspace/repo; \
    apply_one() { pf="$1"; [ -s "$pf" ] || return 0; \
      patch -p1 -i "$pf" 2>/dev/null && return 0; \
      git apply --3way --ignore-whitespace "$pf" 2>/dev/null && return 0; \
      git apply --ignore-whitespace --whitespace=nowarn "$pf" 2>/dev/null && return 0; \
      patch -p1 --fuzz=5 -i "$pf" 2>/dev/null && return 0; \
      git ls-files -z 2>/dev/null | xargs -0 -r sed -i 's/\r$//' 2>/dev/null; \
      git apply --3way --ignore-whitespace "$pf" 2>/dev/null && return 0; \
      patch -p1 --fuzz=5 -i "$pf" 2>/dev/null && return 0; \
      return 0; }; \
    apply_one /tmp/__golden.patch; \
    apply_one /tmp/__test.patch; \
    (cargo fetch 2>&1 | tail -3 || true); \
    (cargo build --tests 2>&1 | tail -5 || true); \
    (cargo test --no-run 2>&1 | tail -5 || true); \
    (cargo nextest run --no-run 2>&1 | tail -5 || true); \
    git checkout -- . 2>/dev/null || true; \
    git clean -fdq 2>/dev/null || true; \
    rm -f /tmp/__golden.patch /tmp/__test.patch
ENV CARGO_NET_OFFLINE=true
# <<< offline-cargo-bake (new4261) <<<

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
