FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV GRADLE_OPTS="-Dorg.gradle.daemon=false"

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    openjdk-17-jdk \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH="${JAVA_HOME}/bin:${PATH}"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/mongodb/mongo-java-driver.git && \
    git fetch --depth 30 origin a03649cb24456f53db45e3a332c99d40a124748e && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN chmod +x gradlew && ./gradlew :driver-scala:dependencies --no-daemon 2>&1 | tail -5

RUN echo 'allprojects { tasks.withType(Test) { testLogging { events "passed", "failed", "skipped" } } }' > /workspace/testlogging.gradle

WORKDIR /workspace

# OFFLINE-BAKE: warm gradle caches by running the test command at build time (with network)
RUN cat > /tmp/offline_warm.sh <<'OFFLINE_WARM_EOF_8f3a'
cd /workspace/repo && ./gradlew :driver-scala:test --tests "org.mongodb.scala.ObservableImplicitsToGridFSUploadPublisherUnitSpec" --tests "org.mongodb.scala.gridfs.GridFSUploadPublisherSpec" --tests "org.mongodb.scala.internal.UnitObservableSpec" && find /workspace/repo/driver-scala/build/test-results/test -name "TEST-*.xml" -exec cat {} \;
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
