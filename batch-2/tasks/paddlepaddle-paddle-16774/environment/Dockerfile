FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git \
    ca-certificates \
    python3 \
    python3-pytest \
    patch \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/PaddlePaddle/Paddle.git && \
    git fetch --depth 30 origin 2e07c19a9c8ef969695de6208a2cff3af675057a && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN cat > /tmp/test_null_safety.py << 'PYEOF'
import os
import pytest

BASE = '/workspace/repo'

def read_file(relpath):
    with open(os.path.join(BASE, relpath)) as f:
        return f.read()

def test_block_null_init():
    content = read_file('paddle/fluid/framework/op_desc.cc')
    assert 'block_ = nullptr' in content

def test_api_null_check():
    content = read_file('paddle/fluid/inference/api/api.cc')
    assert 'other.data() == nullptr' in content

def test_api_impl_null_check():
    content = read_file('paddle/fluid/inference/api/api_impl.cc')
    assert 'PADDLE_ENFORCE_NOT_NULL(dynamic_cast' in content

def test_predictor_null_check():
    content = read_file('paddle/fluid/inference/api/analysis_predictor.cc')
    assert 'PADDLE_ENFORCE_NOT_NULL(input_ptr)' in content

def test_gpc_box_check():
    content = read_file('paddle/fluid/operators/detection/gpc.cc')
    assert 'PADDLE_ENFORCE_NOT_NULL(box)' in content

def test_l2_dist_check():
    content = read_file('paddle/fluid/operators/squared_l2_distance_op.h')
    assert 'PADDLE_ENFORCE_NOT_NULL(x_g)' in content

def test_seq_conv1_data_check():
    content = read_file('paddle/fluid/inference/tests/api/analyzer_seq_conv1_tester.cc')
    assert 'PADDLE_ENFORCE(data.size() >= 4)' in content
PYEOF

WORKDIR /workspace/repo

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# --- offline-harbor: ensure go toolchain present on login PATH (client-req contract) ---
RUN command -v go >/dev/null 2>&1 \
    || (apt-get update && apt-get install -y --no-install-recommends golang-go) \
    || (apt-get update && apt-get install -y --no-install-recommends golang) \
    || true
ENV PATH="/usr/local/go/bin:${PATH}"

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
