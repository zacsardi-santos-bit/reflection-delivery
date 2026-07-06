FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64

RUN apt-get update && apt-get install -y \
    openjdk-21-jdk \
    maven \
    git \
    curl \
    ca-certificates \
    patch \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/instancio/instancio.git && \
    git fetch --depth 30 origin 152c1cf10c1e888779da27e1e76bbf973857347c && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIG12biBpbnN0YWxsIC1EbWF2ZW4udGVzdC5za2lwPXRydWUgLUIgJiYgbXZuIC1wbCBpbnN0YW5jaW8tdGVzdHMvZmVhdHVyZS10ZXN0cyB0ZXN0IC1CICYmIG12biAtcGwgaW5zdGFuY2lvLXRlc3RzL2luc3RhbmNpby1jb3JlLXRlc3RzIC1EdGVzdD1TZXR0aW5nc1N1cHBvcnRUZXN0LFVuaWNvZGVCbG9ja3NUZXN0IC1Ec3VyZWZpcmUuZmFpbElmTm9TcGVjaWZpZWRUZXN0cz1mYWxzZSB0ZXN0IC1C | base64 -d > /workspace/.offline_warm.sh && \
    ( cd /workspace/repo && mvn -B -fn -q -DskipTests -pl 'instancio-tests/feature-tests' -am install -Dcheckstyle.skip=true -Denforcer.skip=true -Dspotbugs.skip=true -Dpmd.skip=true -Dforbiddenapis.skip=true -Dmaven.javadoc.skip=true -Danimal.sniffer.skip=true -Drat.skip=true -Dlicense.skip=true -Dgpg.skip=true -Djacoco.skip=true 2>/dev/null || true ); \
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
