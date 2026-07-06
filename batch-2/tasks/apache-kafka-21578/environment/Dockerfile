FROM eclipse-temurin:21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/kafka.git && \
    git fetch --depth 30 origin 783e50137787d195ae6551149d2df8603d8e8f99 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

ENV GRADLE_USER_HOME=/root/.gradle

RUN ./gradlew :streams:testClasses --no-daemon --no-watch-fs 2>&1 | tail -30 || true


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

# offline-harbor: warm the FULL test-runtime classpath (junit-jupiter-engine,
# objenesis, launcher, etc.) by running the exact grader test task(s) at BUILD
# time (network available here) so offline test execution finds all deps cached.
RUN cd /workspace/repo && ./gradlew :streams:test --tests "*AbstractColumnFamilyAccessorTest" --tests "*DualColumnFamilyAccessorTest" --tests "*RocksDBGenericOptionsToDbOptionsColumnFamilyOptionsAdapterTest" --tests "*RocksDBMigratingSessionStoreWithHeadersTest" --tests "*RocksDBStoreTest" --tests "*RocksDBTimestampedStoreTest" --tests "*RocksDBTimestampedStoreWithHeadersTest" --no-daemon --no-watch-fs -x checkstyleMain -x checkstyleTest -x spotbugsMain -x spotbugsTest || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true
# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` (the client
# verifier exec) keeps go/cargo/java/etc on PATH after /etc/profile resets it.
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
