FROM debian:bookworm-slim

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch \
    python3 python3-pytest \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/envoyproxy/envoy.git && \
    git fetch --depth 30 origin b979155f1aff38488b6d33d0e1f2f94f5468d5dd && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN echo 'import os' > /workspace/test_headers.py && \
    echo 'MESH = "/workspace/repo/contrib/kafka/filters/network/source/mesh"' >> /workspace/test_headers.py && \
    echo 'def test_outbound_record_exists():' >> /workspace/test_headers.py && \
    echo '    assert os.path.exists(MESH + "/outbound_record.h"), "outbound_record.h missing"' >> /workspace/test_headers.py && \
    echo 'def test_outbound_record_has_headers_field():' >> /workspace/test_headers.py && \
    echo '    p = MESH + "/outbound_record.h"' >> /workspace/test_headers.py && \
    echo '    assert os.path.exists(p), p + " does not exist"' >> /workspace/test_headers.py && \
    echo '    assert "headers_" in open(p).read(), "headers_ field missing"' >> /workspace/test_headers.py && \
    echo 'def test_upstream_client_impl_has_convert_headers():' >> /workspace/test_headers.py && \
    echo '    p = MESH + "/upstream_kafka_client_impl.h"' >> /workspace/test_headers.py && \
    echo '    assert os.path.exists(p), p + " does not exist"' >> /workspace/test_headers.py && \
    echo '    assert "convertHeaders" in open(p).read(), "convertHeaders missing"' >> /workspace/test_headers.py

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
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# --- offline toolchain fix: ensure the language toolchain (go) is installed on the login PATH ---
RUN command -v go >/dev/null 2>&1 \
    || (apt-get update && apt-get install -y --no-install-recommends golang-go && rm -rf /var/lib/apt/lists/*) \
    || (apk add --no-cache go) || (dnf install -y golang) || (yum install -y golang) || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
