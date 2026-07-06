FROM gradle:8.5-jdk17

USER root

ENV GRADLE_USER_HOME=/root/.gradle
ENV ANDROID_HOME=/opt/android-sdk
ENV ANDROID_SDK_ROOT=/opt/android-sdk
ENV PATH=${PATH}:${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch unzip wget python3 \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p ${ANDROID_HOME}/cmdline-tools && \
    wget -q https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -O /tmp/cmdtools.zip && \
    unzip -q /tmp/cmdtools.zip -d /tmp && \
    mv /tmp/cmdline-tools ${ANDROID_HOME}/cmdline-tools/latest && \
    rm /tmp/cmdtools.zip

RUN mkdir -p ${ANDROID_HOME}/licenses && \
    printf "8933bad161af4178b1185d1a37fbf41ea5269c55\nd56f5187479451eabf01fb78af6dfcb131a6481e\n24333f8a63b6825ea9c5514f83c2829b004d1fee\n" > ${ANDROID_HOME}/licenses/android-sdk-license && \
    printf "84831b9409646a918e30573bab4c9c91346d8abd\nd975f751698a77b662f1254ddbeed3901e976f5a\n" > ${ANDROID_HOME}/licenses/android-sdk-preview-license && \
    sdkmanager "platforms;android-34" "build-tools;34.0.0" 2>&1 | tail -5

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/lihenggui/blocker.git && \
    git fetch --depth 30 origin 7beb26a6bae010945c35de24892ac33d102ddb02 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN mkdir -p ${GRADLE_USER_HOME} && \
    echo "org.gradle.daemon=false" > ${GRADLE_USER_HOME}/gradle.properties && \
    echo "org.gradle.parallel=false" >> ${GRADLE_USER_HOME}/gradle.properties && \
    echo "warningsAsErrors=false" >> ${GRADLE_USER_HOME}/gradle.properties && \
    echo "org.gradle.configuration-cache=false" >> ${GRADLE_USER_HOME}/gradle.properties && \
    echo "kotlin.incremental=false" >> ${GRADLE_USER_HOME}/gradle.properties

RUN ./gradlew :core:domain:dependencies --no-daemon -q 2>&1 | tail -10 || true

WORKDIR /workspace

CMD ["tail", "-f", "/dev/null"]

# OFFLINE-BAKE: warm gradle caches by running the test command at build time (with network)
RUN cat > /tmp/offline_warm.sh <<'OFFLINE_WARM_EOF_8f3a'
cd /workspace/repo && ./gradlew :core:domain:testFossDebugUnitTest --tests "com.merxury.blocker.core.domain.components.SearchComponentsUseCaseTest" --no-daemon --no-watch-fs && cat core/domain/build/test-results/testFossDebugUnitTest/*.xml
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
