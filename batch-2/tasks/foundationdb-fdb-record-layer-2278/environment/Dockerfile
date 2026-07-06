FROM eclipse-temurin:11-jdk-jammy

RUN apt-get update && apt-get install -y git curl ca-certificates patch python3 && rm -rf /var/lib/apt/lists/*

RUN curl -L -o /usr/local/bin/fdbserver "https://github.com/apple/foundationdb/releases/download/7.1.26/fdbserver.x86_64" \
    && curl -L -o /usr/local/bin/fdbcli "https://github.com/apple/foundationdb/releases/download/7.1.26/fdbcli.x86_64" \
    && curl -L -o /usr/local/lib/libfdb_c.so "https://github.com/apple/foundationdb/releases/download/7.1.26/libfdb_c.x86_64.so" \
    && chmod +x /usr/local/bin/fdbserver /usr/local/bin/fdbcli \
    && ldconfig

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/FoundationDB/fdb-record-layer.git && \
    git fetch --depth 30 origin ee43d69b4baf6334a4c337ea60ea4286a60e7462 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN ./gradlew :fdb-record-layer-core:dependencies --no-daemon --no-watch-fs 2>&1 | tail -30 || true

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

# --- offline-harbor: gradle offline-bake (auto) ---
# Set daemon off + write gradle.properties knobs into GRADLE_USER_HOME (default /root/.gradle).
RUN GUH="${GRADLE_USER_HOME:-/root/.gradle}"; mkdir -p "$GUH"; \
    touch "$GUH/gradle.properties"; \
    grep -q '^org.gradle.daemon=' "$GUH/gradle.properties" || printf 'org.gradle.daemon=false\n' >> "$GUH/gradle.properties"
# Warm ~/.gradle by running the EXACT test command at build time (network ON here).
RUN timeout 3500 bash -c 'mkdir -p /var/lib/fdb/data /var/log/fdb && echo '\''docker:docker@127.0.0.1:4500'\'' > /etc/fdb.cluster && fdbserver -p 127.0.0.1:4500 -l 127.0.0.1:4500 -d /var/lib/fdb/data -L /var/log/fdb -C /etc/fdb.cluster & sleep 10 && (fdbcli -C /etc/fdb.cluster --exec '\''configure new single memory; status'\'' --timeout 30 || true) && printf '\''FDB_CLUSTER_FILE=/etc/fdb.cluster\n'\'' > /workspace/repo/fdb-environment.properties && cd /workspace/repo && (./gradlew :fdb-record-layer-core:test --tests '\''*VersionIndexTest'\'' --tests '\''*FDBMetaDataStoreTest'\'' --tests '\''*FDBRecordStoreOpeningTest'\'' --tests '\''*FDBRecordStoreTest'\'' --no-daemon --no-watch-fs; GRADLE_EXIT=$?; find . -name '\''TEST-*.xml'\'' 2>/dev/null | xargs cat 2>/dev/null || true; exit $GRADLE_EXIT)' > /tmp/gradle_warm.log 2>&1 || true; tail -n 20 /tmp/gradle_warm.log 2>/dev/null || true
# Broader fallback warm in case the exact command's project layout differs.
RUN cd /workspace/repo 2>/dev/null && ( ./gradlew --no-daemon testClasses > /tmp/gradle_warm2.log 2>&1 \
      || ./gradlew --no-daemon compileTestJava >> /tmp/gradle_warm2.log 2>&1 \
      || ./gradlew --no-daemon build -x test >> /tmp/gradle_warm2.log 2>&1 \
      || gradle --no-daemon testClasses >> /tmp/gradle_warm2.log 2>&1 || true ); \
    tail -n 10 /tmp/gradle_warm2.log 2>/dev/null || true
# CRITICAL: remove stale test-results/reports produced by the build-time warm so the
# grader cannot read pre-baked passing XML (would otherwise leak nop reward). The
# downloaded dependency cache lives in $GRADLE_USER_HOME and is untouched.
RUN find /workspace/repo -type d \( -name 'test-results' -o -name 'reports' \) -path '*/build/*' -prune -exec rm -rf {} + 2>/dev/null || true
# Force gradle OFFLINE at test time via an init script (handles custom GRADLE_USER_HOME).
RUN GUH="${GRADLE_USER_HOME:-/root/.gradle}"; mkdir -p "$GUH/init.d"; \
    printf 'gradle.startParameter.offline = true\n' > "$GUH/init.d/zzz-offline.gradle"

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
