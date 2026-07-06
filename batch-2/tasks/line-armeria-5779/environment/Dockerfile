FROM eclipse-temurin:21-jdk-jammy

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/line/armeria.git && \
    git fetch --depth 1 origin 242d0296d1adfb4a746e7c1842eaa3c036747ebc && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN ./gradlew :core:testClasses :xds:testClasses --no-daemon --no-watch-fs 2>&1 | tail -20 || true

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
RUN timeout 3500 bash -c 'cd /workspace/repo && ./gradlew :core:clean :xds:clean --no-daemon --no-watch-fs && ./gradlew :core:test --tests "*WeightRampingUpStrategyIntegrationTest" --tests "*WeightedRoundRobinStrategyTest" --tests "*RampingUpLoadBalancerTest" --tests "*RoundRobinLoadBalancerTest" --tests "*StickyLoadBalancerTest" --tests "*WeightTransitionTest" --tests "*WeightedRandomLoadBalancerTest" --tests "*WeightedRoundRobinLoadBalancerTest" --no-daemon --no-watch-fs && ./gradlew :xds:test --tests "*RampingUpTest" --no-daemon --no-watch-fs && find /workspace/repo/core/build/test-results -name '\''TEST-*.xml'\'' -exec cat {} \; 2>/dev/null && find /workspace/repo/xds/build/test-results -name '\''TEST-*.xml'\'' -exec cat {} \; 2>/dev/null' > /tmp/gradle_warm.log 2>&1 || true; tail -n 20 /tmp/gradle_warm.log 2>/dev/null || true
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

# OFFLINE-SIZE: keep .git < 100MB by dropping large binary/media blobs from the object store.
# Commit/tree objects and all small source blobs are retained, so HEAD stays == base_commit,
# git remains present/valid (rev-parse/rev-list work) and source checkout/apply still function.
RUN cd /workspace/repo 2>/dev/null && [ -d .git ] && { \
      git repack -ad 2>/dev/null || true; git reflog expire --expire=now --all 2>/dev/null || true; \
      git gc --prune=now 2>/dev/null || true; \
      if [ "$(du -sm .git 2>/dev/null | cut -f1)" -ge 100 ]; then \
        git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype) %(objectsize)' > /tmp/_ao 2>/dev/null; \
        for TH in 1000000 300000 150000 80000 40000 20000; do \
          awk -v th=$TH '!($2=="blob" && $3>th){print $1}' /tmp/_ao > /tmp/_keep; \
          git pack-objects --stdout < /tmp/_keep > /tmp/_np.pack 2>/dev/null || break; \
          rm -f .git/objects/pack/*; mv /tmp/_np.pack .git/objects/pack/pack-shrunk.pack; \
          ( cd .git/objects/pack && git index-pack pack-shrunk.pack >/dev/null 2>&1 ); \
          [ "$(du -sm .git 2>/dev/null | cut -f1)" -lt 90 ] && break; \
        done; \
        rm -f /tmp/_ao /tmp/_keep; \
        git reflog expire --expire=now --all 2>/dev/null || true; git gc --prune=now 2>/dev/null || true; \
      fi; \
    } || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
