FROM eclipse-temurin:17

RUN apt-get update && apt-get install -y git ca-certificates patch python3 && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /root/.gradle/init.d && printf 'allprojects {\n  tasks.withType(Test) {\n    testLogging {\n      events "passed", "failed", "skipped"\n      exceptionFormat "full"\n    }\n  }\n}\n' > /root/.gradle/init.d/test-logging.gradle

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/Consensys/teku.git && \
    git fetch --depth 1 origin 052368b114f80da48e4617505cca0021925f89b8 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN ./gradlew :beacon:pow:testClasses :services:powchain:testClasses :teku:testClasses --no-daemon --no-watch-fs || true
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIC4vZ3JhZGxldyA6YmVhY29uOnBvdzp0ZXN0IDpzZXJ2aWNlczpwb3djaGFpbjp0ZXN0IDp0ZWt1OnRlc3QgLS10ZXN0cyAiKkRlcG9zaXRTbmFwc2hvdEZpbGVMb2FkZXJUZXN0IiAtLXRlc3RzICIqRGVwb3NpdFNuYXBzaG90c0J1bmRsZVRlc3QiIC0tdGVzdHMgIipQb3djaGFpbkNvbmZpZ3VyYXRpb25UZXN0IiAtLXRlc3RzICIqUG93Y2hhaW5TZXJ2aWNlVGVzdCIgLS10ZXN0cyAiKkRlcG9zaXRPcHRpb25zVGVzdCIgLS1uby1kYWVtb24gLS1uby13YXRjaC1mcyAmJiBweXRob24zIC1jICdpbXBvcnQgeG1sLmV0cmVlLkVsZW1lbnRUcmVlIGFzIEVULGdsb2I7WyhwcmludCgiUnVubmluZyAiK3IuZ2V0KCJuYW1lIiwiIikpLHByaW50KCJUZXN0cyBydW46ICIrci5nZXQoInRlc3RzIiwiMCIpKyIsIEZhaWx1cmVzOiAiK3IuZ2V0KCJmYWlsdXJlcyIsIjAiKSsiLCBFcnJvcnM6ICIrci5nZXQoImVycm9ycyIsIjAiKSsiLCBTa2lwcGVkOiAiK3IuZ2V0KCJza2lwcGVkIiwiMCIpKSkgZm9yIGYgaW4gc29ydGVkKGdsb2IuZ2xvYigiKiovdGVzdC1yZXN1bHRzL3Rlc3QvVEVTVC0qLnhtbCIscmVjdXJzaXZlPVRydWUpKSBmb3IgciBpbiBbRVQucGFyc2UoZikuZ2V0cm9vdCgpXV0n | base64 -d > /workspace/.offline_warm.sh && \
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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH

# dv01-blobdrop: shrink .git<100MB by dropping blob objects (working tree kept; grade uses patch -p1)
RUN cd /workspace/repo && \
    git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_nb.txt && \
    ( cat /tmp/_nb.txt | git pack-objects --non-empty /tmp/_gcpk ) && \
    rm -f .git/objects/pack/* && cp /tmp/_gcpk-*.pack /tmp/_gcpk-*.idx .git/objects/pack/ && \
    rm -f /tmp/_nb.txt /tmp/_gcpk-* && \
    (git reflog expire --expire=now --all 2>/dev/null || true) && rm -rf .git/logs && \
    (git remote remove origin 2>/dev/null || true) && (git tag -l | xargs -r git tag -d 2>/dev/null || true) && \
    (git gc --prune=now 2>/dev/null || true)

RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
