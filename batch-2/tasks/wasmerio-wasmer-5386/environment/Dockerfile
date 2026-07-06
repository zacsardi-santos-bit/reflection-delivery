FROM rust:1.81

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential pkg-config \
    && rm -rf /var/lib/apt/lists/*

RUN curl -LsSf https://get.nexte.st/latest/linux | tar zxf - -C /usr/local/bin

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/wasmerio/wasmer.git && \
    git fetch --depth 1 origin 1261f7945a874d176722c7d2911cd5ab65f7d8f6 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN cargo fetch

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

# >>> git-slim (compliance): shrink .git<100MB by dropping blob objects, keep commit+tree >>>
# Working-tree files stay on disk (untouched) so patches/tests still work; git metadata
# is preserved (HEAD==base, shallow). Rebuild pack from non-blob objects only.
RUN cd /workspace/repo && ( \
    git remote remove origin 2>/dev/null || true; \
    git tag -l 2>/dev/null | xargs -r git tag -d 2>/dev/null || true; \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d 2>/dev/null || true; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    NB=$(mktemp); \
    git cat-file --batch-all-objects --batch-check 2>/dev/null | awk '$2!="blob"{print $1}' > "$NB"; \
    mkdir -p .git/objects/pack.new; \
    if [ -s "$NB" ]; then git pack-objects --no-reuse-delta .git/objects/pack.new/pack < "$NB" >/dev/null 2>&1 || true; fi; \
    rm -rf .git/objects/pack; mv .git/objects/pack.new .git/objects/pack; \
    find .git/objects -type d -regextype posix-egrep -regex '.*/[0-9a-f]{2}$' -exec rm -rf {} + 2>/dev/null || true; \
    rm -rf .git/logs; \
    rm -f "$NB"; \
    git remote remove origin 2>/dev/null || true; \
    git tag -l 2>/dev/null | xargs -r git tag -d 2>/dev/null || true; \
    true )
# <<< git-slim (compliance) <<<

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
