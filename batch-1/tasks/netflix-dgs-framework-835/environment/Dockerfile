FROM gradle:7.3.3-jdk8

USER root
RUN apt-get update && apt-get install -y python3 patch git && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/Netflix/dgs-framework.git && \
    git fetch --depth 30 origin 51c7f22953464d06db2e09c70c894c10ffec4789 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN chown -R root:root /workspace

WORKDIR /workspace/repo

RUN sed -i 's/id("nebula.netflixoss") version "10.5.1"/\/\/ id("nebula.netflixoss") version "10.5.1"/g' build.gradle.kts && \
    sed -i 's/apply(plugin = "nebula.netflixoss")/\/\/ apply(plugin = "nebula.netflixoss")/g' build.gradle.kts && \
    sed -i '86,92s/^/\/\/ /' build.gradle.kts

ENV GRADLE_USER_HOME=/root/.gradle
ENV HOME=/root
RUN ./gradlew :graphql-dgs:dependencies --no-daemon || true

WORKDIR /workspace

RUN touch /root/.bashrc && chmod 755 /root
CMD ["sleep", "infinity"]

# OFFLINE-BAKE: warm gradle caches by running the test command at build time (with network)
RUN cat > /tmp/offline_warm.sh <<'OFFLINE_WARM_EOF_8f3a'
cd /workspace/repo && sed -i 's/id("nebula.netflixoss") version "10.5.1"/\/\/ id("nebula.netflixoss") version "10.5.1"/g' build.gradle.kts && sed -i 's/apply(plugin = "nebula.netflixoss")/\/\/ apply(plugin = "nebula.netflixoss")/g' build.gradle.kts && sed -i '86,92s/^/\/\/ /' build.gradle.kts && GRADLE_OPTS='-Xmx512m' ./gradlew :graphql-dgs:test --tests "*DefaultDgsQueryExecutorTest" --no-daemon --no-watch-fs --max-workers=1; for f in /workspace/repo/graphql-dgs/build/test-results/test/*.xml; do echo 'START_JUNIT_XML'; grep -E "<testcase|<failure|</testcase|<testsuite|</testsuite" "$f" || cat "$f" | head -100; done
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
