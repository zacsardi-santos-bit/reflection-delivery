FROM eclipse-temurin:17-jdk-jammy

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/zaproxy/zaproxy.git && \
    git fetch --depth 30 origin b2aed8b5005495af6177a1ad92645d30d126f811 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

ENV GRADLE_OPTS="-Xmx512m -Xms128m"

RUN mkdir -p /root/.gradle/init.d && printf 'allprojects {\n    tasks.withType(Test) {\n        testLogging {\n            events "passed", "failed", "skipped"\n        }\n    }\n}\n' > /root/.gradle/init.d/test-logging.gradle

RUN ./gradlew dependencies --no-daemon --no-watch-fs 2>&1 | tail -5 || true

RUN ./gradlew :zap:testClasses --no-daemon --no-watch-fs 2>&1 | tail -5 || true

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
RUN timeout 3500 bash -c 'cd /workspace/repo && ./gradlew :zap:test --tests "*SessionUnitTest" --tests "*SiteMapUnitTest" --tests "*ContextUnitTest" --no-daemon --no-watch-fs && find zap/build/test-results -name "TEST-*.xml" -exec cat {} \;' > /tmp/gradle_warm.log 2>&1 || true; tail -n 20 /tmp/gradle_warm.log 2>/dev/null || true
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
# Prefetch golden/test-introduced gradle deps (+transitive closure) into the cache.
RUN set -e; mkdir -p /tmp/depwarm; \
    printf 'rootProject.name="depwarm"\n' > /tmp/depwarm/settings.gradle; \
    { printf 'buildscript {\n  repositories { mavenCentral(); google(); maven { url \042https://plugins.gradle.org/m2/\042 }; gradlePluginPortal() }\n  dependencies {\n'; \
      printf '    classpath \042com.formdev:flatlaf:3.2.5\042\n'; \
      printf '  }\n}\nrepositories {\n  mavenCentral()\n  google()\n  maven { url \042https://maven-central.storage-download.googleapis.com/repos/central/data/\042 }\n  maven { url \042https://plugins.gradle.org/m2/\042 }\n  gradlePluginPortal()\n}\n'; \
      printf 'task resolveWarm { doLast { [\042com.formdev:flatlaf:3.2.5\042].each { gav -> try { def d = dependencies.create(gav); def c = configurations.detachedConfiguration(d); c.transitive = true; c.resolve() } catch(e) { logger.lifecycle(\042warm-skip \042 + gav) } } } }\n'; \
    } > /tmp/depwarm/build.gradle; \
    ( cd /tmp/depwarm && ( gradle --no-daemon resolveWarm > /tmp/depwarm.log 2>&1 \
        || /workspace/repo/gradlew --no-daemon -p /tmp/depwarm resolveWarm >> /tmp/depwarm.log 2>&1 \
        || true ) ); tail -n 20 /tmp/depwarm.log 2>/dev/null || true; true
# Force gradle OFFLINE at test time via an init script (handles custom GRADLE_USER_HOME).
RUN GUH="${GRADLE_USER_HOME:-/root/.gradle}"; mkdir -p "$GUH/init.d"; \
    printf 'gradle.startParameter.offline = true\n' > "$GUH/init.d/zzz-offline.gradle"

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
