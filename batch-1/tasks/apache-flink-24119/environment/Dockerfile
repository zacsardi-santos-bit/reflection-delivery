FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch \
    openjdk-17-jdk maven \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/flink.git && \
    git fetch --depth 30 origin 201571b486f405358a31e077247241892d537198 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIG12biAtcGwgZmxpbmstY29yZSxmbGluay1ydW50aW1lLGZsaW5rLXN0YXRlLWJhY2tlbmRzL2ZsaW5rLXN0YXRlYmFja2VuZC1yb2Nrc2RiLGZsaW5rLXN0cmVhbWluZy1qYXZhIC1hbSB0ZXN0IC1EZW5mb3JjZXIuc2tpcD10cnVlIC1EdGVzdD1GaWxlVXRpbHNUZXN0I3Rlc3RHZXREaXJlY3RvcnlTaXplLEluY3JlbWVudGFsTG9jYWxLZXllZFN0YXRlSGFuZGxlVGVzdCxJbmNyZW1lbnRhbFJlbW90ZUtleWVkU3RhdGVIYW5kbGVUZXN0I3Rlc3RDb2xsZWN0U2l6ZVN0YXRzLEtleUdyb3Vwc1N0YXRlSGFuZGxlVGVzdCN0ZXN0Q29sbGVjdFNpemVTdGF0cyxGaWxlU3RhdGVIYW5kbGVUZXN0I3Rlc3RDb2xsZWN0U2l6ZVN0YXRzLEJ5dGVTdHJlYW1TdGF0ZUhhbmRsZVRlc3QjdGVzdENvbGxlY3RTaXplU3RhdHMsUm9ja3NJbmNyZW1lbnRhbFNuYXBzaG90U3RyYXRlZ3lUZXN0I3Rlc3RDaGVja3BvaW50SXNJbmNyZW1lbnRhbCxCYWNrZW5kUmVzdG9yZXJQcm9jZWR1cmVUZXN0LFN0cmVhbVRhc2tTdGF0ZUluaXRpYWxpemVySW1wbFRlc3QgLURzdXJlZmlyZS5mYWlsSWZOb1NwZWNpZmllZFRlc3RzPWZhbHNl | base64 -d > /workspace/.offline_warm.sh && \
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
