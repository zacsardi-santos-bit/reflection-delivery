FROM ubuntu:24.04

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential openjdk-17-jdk \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/gravitino.git && \
    git fetch --depth 30 origin 73ab2ec9fab249ab845e57ce0f3fe1715b9d9dcb && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN ./gradlew :core:compileTestJava --no-daemon --no-watch-fs 2>&1 | tail -30 || true

RUN printf 'allprojects {\n  tasks.withType(Test).configureEach {\n    testLogging {\n      events "passed", "failed", "skipped"\n    }\n    afterSuite { d, r ->\n      if (d.className != null) {\n        println "[INFO] Tests run: ${r.testCount}, Failures: ${r.failedTestCount}, Errors: 0, Skipped: ${r.skippedTestCount} - in ${d.className}"\n      }\n    }\n  }\n}\n' > /workspace/test-events.gradle
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIC4vZ3JhZGxldyA6Y29yZTp0ZXN0IC0tdGVzdHMgIipUZXN0RW50aXR5Q2FjaGVLZXkiIC0tbm8tZGFlbW9uIC0tbm8td2F0Y2gtZnMgLS1pbml0LXNjcmlwdCAvd29ya3NwYWNlL3Rlc3QtZXZlbnRzLmdyYWRsZQ== | base64 -d > /workspace/.offline_warm.sh && \
    (timeout 3000 bash /workspace/.offline_warm.sh 2>&1 | tail -25 || true)

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
