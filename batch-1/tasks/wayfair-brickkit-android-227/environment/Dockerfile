FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch openjdk-17-jdk wget unzip \
    && rm -rf /var/lib/apt/lists/*

RUN wget -q https://github.com/JetBrains/kotlin/releases/download/v1.9.22/kotlin-compiler-1.9.22.zip \
    -O /tmp/kotlin.zip && unzip -q /tmp/kotlin.zip -d /opt && rm /tmp/kotlin.zip

ENV PATH=$PATH:/opt/kotlinc/bin

RUN wget -q "https://repo1.maven.org/maven2/junit/junit/4.13/junit-4.13.jar" -O /opt/junit.jar && \
    wget -q "https://repo1.maven.org/maven2/org/hamcrest/hamcrest-core/1.3/hamcrest-core-1.3.jar" -O /opt/hamcrest.jar && \
    wget -q "https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.9.3/junit-platform-console-standalone-1.9.3.jar" -O /opt/junit-console.jar

RUN mkdir -p /tmp/android-src/android/os && \
    printf 'package android.os;\npublic class Looper {\n  private static final Looper sMain = new Looper();\n  public static Looper getMainLooper() { return sMain; }\n}\n' \
        > /tmp/android-src/android/os/Looper.java && \
    printf 'package android.os;\npublic class Handler {\n  public Handler(Looper l) {}\n  public boolean post(Runnable r) { r.run(); return true; }\n}\n' \
        > /tmp/android-src/android/os/Handler.java && \
    javac /tmp/android-src/android/os/Looper.java /tmp/android-src/android/os/Handler.java \
        -d /tmp/android-classes && \
    jar cf /opt/android-stubs.jar -C /tmp/android-classes . && \
    rm -rf /tmp/android-src /tmp/android-classes

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/wayfair/brickkit-android.git && \
    git fetch --depth 30 origin 6d1f4c5d104738902a4f0822380a18d92e7a325c && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

WORKDIR /workspace

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
