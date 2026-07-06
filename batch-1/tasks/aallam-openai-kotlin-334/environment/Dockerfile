FROM eclipse-temurin:11-jdk

RUN apt-get update && apt-get install -y git patch curl && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/aallam/openai-kotlin.git && \
    git fetch --depth 30 origin 3ecab2eafcb7f6ac169812b0469cdfe3a48d662f && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

ENV OPENAI_API_KEY=fake-key-for-testing
ENV GRADLE_OPTS="-Dorg.gradle.jvmargs=-Xmx2g -Dorg.gradle.daemon=false"

RUN ./gradlew :openai-client:dependencies --no-daemon --no-watch-fs 2>&1 | tail -20 || true

RUN ./gradlew :openai-client:compileKotlinJvm :openai-client:compileTestKotlinJvm --no-daemon --no-watch-fs 2>&1 | tail -20 || true

WORKDIR /workspace

# OFFLINE-BAKE: warm gradle caches by running the test command at build time (with network)
RUN cat > /tmp/offline_warm.sh <<'OFFLINE_WARM_EOF_8f3a'
bash -c "cd /workspace/repo && ./gradlew :openai-client:jvmTest --tests '*.TestBatches.batchSerialization' --tests '*.TestBatches.testDecodeOutput' --no-daemon --no-watch-fs; EXIT_CODE=\$?; find . -path '*/build/test-results/*/TEST-*.xml' 2>/dev/null | xargs cat 2>/dev/null; exit \$EXIT_CODE"
OFFLINE_WARM_EOF_8f3a
RUN cd /workspace/repo && (bash /tmp/offline_warm.sh 2>&1 | tail -40 || true)

# OFFLINE-BAKE: force gradle offline at test time (no network in sandbox)
RUN GUH="${GRADLE_USER_HOME:-$HOME/.gradle}" && mkdir -p "$GUH/init.d" && \
    printf 'gradle.startParameter.offline = true\n' > "$GUH/init.d/zzz-offline.gradle"

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
