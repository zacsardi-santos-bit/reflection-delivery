FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch \
    && rm -rf /var/lib/apt/lists/*

RUN pip install pytest --break-system-packages

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/grpc/grpc.git && \
    git fetch --depth 1 origin c73c72477833827621de5bcb5863feaf2ab3a302 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

RUN cat > /test_bzl.py << 'PYEOF'
import re

BZL = "/workspace/repo/bazel/grpc_build_system.bzl"

def read_bzl():
    with open(BZL) as f:
        return f.read()

def test_is_msvc_defined():
    assert "def is_msvc" in read_bzl()

def test_grpc_cc_binary_supports_tags():
    content = read_bzl()
    m = re.search(r"def grpc_cc_binary\(([^)]+)\)", content, re.DOTALL)
    assert m
    assert "tags" in m.group(1)

def test_grpc_cc_library_supports_tags():
    content = read_bzl()
    m = re.search(r"def grpc_cc_library\(([^)]+)\)", content, re.DOTALL)
    assert m
    assert "tags" in m.group(1)

def test_grpc_cc_test_passes_tags():
    assert '"tags": tags' in read_bzl()

def test_grpc_cc_test_checks_msvc():
    assert "uses_polling and not is_msvc()" in read_bzl()
PYEOF

WORKDIR /workspace/repo

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
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
