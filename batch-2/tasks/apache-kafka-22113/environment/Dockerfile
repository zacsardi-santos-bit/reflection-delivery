FROM eclipse-temurin:21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/kafka.git && \
    git fetch --depth 30 origin a406735f15991b33e14890d90ff32a3f4c40da9e && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN ./gradlew :tools:testClasses --no-daemon --no-watch-fs


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

# --- offline-harbor(fx_09): bake gradle test-graph deps at BUILD time ---
RUN cd /workspace/repo && (./gradlew --version --no-daemon --no-watch-fs 2>&1 | tail -5 || true)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIC4vZ3JhZGxldyA6dG9vbHM6dGVzdCAtLXRlc3RzICIqQ29uZmlnQ29tbWFuZEludGVncmF0aW9uVGVzdCIgLS10ZXN0cyAiKkNvbmZpZ0NvbW1hbmRUZXN0IiAtLW5vLWRhZW1vbiAtLW5vLXdhdGNoLWZzOyBmaW5kIC93b3Jrc3BhY2UvcmVwby90b29scy9idWlsZC90ZXN0LXJlc3VsdHMgLW5hbWUgJ1RFU1QtKi54bWwnIC1leGVjIGNhdCB7fSBcOyAyPi9kZXYvbnVsbCB8fCB0cnVl | base64 -d | bash > /tmp/warm.log 2>&1 || true

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
