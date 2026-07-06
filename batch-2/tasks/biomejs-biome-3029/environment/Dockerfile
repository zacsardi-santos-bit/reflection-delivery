FROM rust:1.78

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential pkg-config \
    && rm -rf /var/lib/apt/lists/*

RUN curl -LsSf https://get.nexte.st/latest/linux | tar zxf - -C /usr/local/bin

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/biomejs/biome.git && \
    git fetch --depth 1 origin bc30892289cfc486e929fd844968eefe4026282d && \
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

RUN cargo build --tests -p biome_cli 2>&1 | tail -10 || true

WORKDIR /workspace

# === offline dep-baking: warm cargo caches at build (has network) ===
# Warm the EXACT dependency graph the golden solution + tests need (build has network),
# then revert so the solution is NOT baked. The crate registry index + crate tarballs live
# under $CARGO_HOME (outside the repo) so they persist after the revert; a generated
# Cargo.lock (when the repo had none) is committed so offline resolution is pinned.
#
# We COPY the test_command as a plain file (offline_warm_cmd.txt) so the warm step never
# depends on python3 being present in the base image at build time. nextest invokes
# `cargo metadata --all-features --filter-platform <host>` at run time, so we mirror that
# (+ cargo fetch) last to guarantee every all-features-only / optional / dev crate is
# downloaded for offline use, even in repos with no committed lockfile.
# [sanitized] removed patch COPY (no solution/test in image).patch
# [sanitized] removed patch COPY (no solution/test in image)
COPY offline_warm_cmd.txt /workspace/offline_warm_cmd.txt
RUN cd /workspace/repo && \
    HAD_LOCK=$( [ -f Cargo.lock ] && echo 1 || echo 0 ) && \
    HOST=$(rustc -vV | sed -n 's/^host: //p') && \
    TEST_CMD=$(cat /workspace/offline_warm_cmd.txt) && \
    (cargo fetch 2>/dev/null || true) && \
    (git apply --3way /workspace/golden.patch 2>/dev/null || git apply /workspace/golden.patch 2>/dev/null || patch -p1 --forward </workspace/golden.patch 2>/dev/null || true) && \
    (git apply --3way /workspace/test.patch 2>/dev/null || git apply /workspace/test.patch 2>/dev/null || patch -p1 --forward </workspace/test.patch 2>/dev/null || true) && \
    (cargo fetch 2>/dev/null || true) && \
    if printf '%s' "$TEST_CMD" | grep -qiE 'cargo[[:space:]]+(nextest[[:space:]]+run|test|check|build)' && ! printf '%s' "$TEST_CMD" | grep -qiE 'cargo[[:space:]]+run|forge[[:space:]]+test|wasm-pack|npx|service[[:space:]]'; then \
        (cd /workspace/repo && timeout 3000 bash -c "$TEST_CMD" >/tmp/warm.log 2>&1 || true); \
    else \
        (cd /workspace/repo && timeout 1500 cargo fetch 2>/dev/null; true); \
        WARM_DIR=$(printf '%s' "$TEST_CMD" | grep -oE 'cd[[:space:]]+/workspace/repo[^[:space:]&|;]*' | head -1 | sed 's/^cd[[:space:]]*//'); \
        ( [ -n "$WARM_DIR" ] && [ -d "$WARM_DIR" ] && cd "$WARM_DIR" && timeout 1500 cargo fetch 2>/dev/null; true ); \
    fi && \
    cd /workspace/repo && \
    (timeout 900 cargo metadata --all-features --filter-platform "$HOST" --format-version 1 >/dev/null 2>&1 || timeout 900 cargo metadata --all-features --format-version 1 >/dev/null 2>&1 || true) && \
    (cargo fetch 2>/dev/null || true) && \
    (cp -f Cargo.lock /tmp/Cargo.lock.warm 2>/dev/null || true) && \
    (git checkout -- . 2>/dev/null || true) && \
    (git clean -fdq 2>/dev/null || true) && \
    ( [ "$HAD_LOCK" = "0" ] && [ -f /tmp/Cargo.lock.warm ] && cp -f /tmp/Cargo.lock.warm Cargo.lock && \
        git add -f Cargo.lock 2>/dev/null && \
        git -c user.email=ci@ci -c user.name=ci commit -q -m "ci: pin offline lockfile" 2>/dev/null || true ) && \
    rm -f /workspace/golden.patch /workspace/test.patch /workspace/offline_warm_cmd.txt /tmp/Cargo.lock.warm
ENV CARGO_NET_OFFLINE=true

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
