FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

RUN apt-get update && apt-get install -y \
    openjdk-11-jdk \
    git curl ca-certificates patch build-essential unzip wget \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
ENV PATH="${JAVA_HOME}/bin:${PATH}"
ENV ANDROID_HOME=/opt/android-sdk
ENV PATH="${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:${PATH}"

RUN mkdir -p /opt/android-sdk/cmdline-tools && \
    wget -q https://dl.google.com/android/repository/commandlinetools-linux-9477386_latest.zip -O /tmp/cmdline-tools.zip && \
    unzip -q /tmp/cmdline-tools.zip -d /tmp/cmdline-extract && \
    mv /tmp/cmdline-extract/cmdline-tools /opt/android-sdk/cmdline-tools/latest && \
    rm -rf /tmp/cmdline-tools.zip /tmp/cmdline-extract

RUN yes | sdkmanager --licenses > /dev/null 2>&1 && \
    sdkmanager "platforms;android-33" "build-tools;33.0.1"

RUN mkdir -p /root/.gradle/init.d && \
    printf 'allprojects {\n    tasks.withType(Test) {\n        testLogging {\n            events "passed", "failed", "skipped"\n            exceptionFormat = "full"\n            showExceptions = true\n            showCauses = true\n            showStackTraces = true\n        }\n    }\n}\n' > /root/.gradle/init.d/verbose-tests.gradle

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/braintree/braintree_android.git && \
    git fetch --depth 30 origin a32642e7d6e74913429467d91e174827779ee313 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN ./gradlew :BraintreeCore:dependencies :Venmo:dependencies --no-daemon || true

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
RUN timeout 3500 bash -c 'cd /workspace/repo && ./gradlew :BraintreeCore:testDebugUnitTest --tests "*BraintreeClientUnitTest" --tests "*ConfigurationCacheUnitTest" --tests "*ConfigurationLoaderUnitTest" --tests "*UUIDHelperUnitTest" --no-daemon --no-watch-fs && ./gradlew :Venmo:testDebugUnitTest --tests "*VenmoClientUnitTest" --tests "*VenmoSharedPrefsWriterUnitTest" --no-daemon --no-watch-fs && find /workspace/repo -path '\''*/build/test-results/testDebugUnitTest/*.xml'\'' -exec cat {} \;' > /tmp/gradle_warm.log 2>&1 || true; tail -n 20 /tmp/gradle_warm.log 2>/dev/null || true
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

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
