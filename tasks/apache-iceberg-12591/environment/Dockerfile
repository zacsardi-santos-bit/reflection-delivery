FROM eclipse-temurin:17-jdk

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/iceberg.git && \
    git fetch --depth 30 origin 2e1577e62bf023daddb0ad7aec5ce6f094fb98dc && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN ./gradlew :core:testClasses :aws:testClasses :gcp:testClasses --no-daemon --no-watch-fs 2>&1 | tail -30 || true
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIC4vZ3JhZGxldyA6aWNlYmVyZy1jb3JlOnRlc3QgLS10ZXN0cyAiKlRlc3RDYXRhbG9nVXRpbCIgLS10ZXN0cyAiKlRlc3RSZXNvbHZpbmdJTyIgLS10ZXN0cyAiKlRlc3RTdG9yYWdlQ3JlZGVudGlhbCIgLS1uby1kYWVtb24gLS1uby13YXRjaC1mcyAmJiAuL2dyYWRsZXcgOmljZWJlcmctZ2NwOnRlc3QgLS10ZXN0cyAiKkdDU0ZpbGVJT1Rlc3QiIC0tbm8tZGFlbW9uIC0tbm8td2F0Y2gtZnMgJiYgcHl0aG9uMyAtYyAiaW1wb3J0IG9zLHhtbC5ldHJlZS5FbGVtZW50VHJlZSBhcyBFVDsgW3ByaW50KCdUZXN0cyBydW46IHt9LCBGYWlsdXJlczoge30sIEVycm9yczoge30sIFNraXBwZWQ6IHt9Jy5mb3JtYXQoaW50KHIuZ2V0KCd0ZXN0cycsMCkpLGludChyLmdldCgnZmFpbHVyZXMnLDApKSxpbnQoci5nZXQoJ2Vycm9ycycsMCkpLGludChyLmdldCgnc2tpcHBlZCcsMCkpKSkgZm9yIGQgaW4gWydjb3JlL2J1aWxkL3Rlc3QtcmVzdWx0cy90ZXN0JywnZ2NwL2J1aWxkL3Rlc3QtcmVzdWx0cy90ZXN0J10gZm9yIGYgaW4gKHNvcnRlZChvcy5saXN0ZGlyKGQpKSBpZiBvcy5wYXRoLmV4aXN0cyhkKSBlbHNlIFtdKSBpZiBmLmVuZHN3aXRoKCcueG1sJykgZm9yIHIgaW4gW0VULnBhcnNlKG9zLnBhdGguam9pbihkLGYpKS5nZXRyb290KCldIGlmIGludChyLmdldCgndGVzdHMnLDApKT4wXSI= | base64 -d > /workspace/.offline_warm.sh && \
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
