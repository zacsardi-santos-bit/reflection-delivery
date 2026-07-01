FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ray-project/ray.git && \
    git fetch --depth 30 origin 6b7328469856d619561abacf60d2b0898fb1a9a8 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install pytest grpcio grpcio-tools starlette protobuf

RUN mkdir -p /workspace/repo/python/ray/serve/generated && \
    python -m grpc_tools.protoc \
        --proto_path=src/ray/protobuf/ \
        --python_out=/workspace/repo/python/ray/serve/generated \
        --grpc_python_out=/workspace/repo/python/ray/serve/generated \
        src/ray/protobuf/serve.proto && \
    touch /workspace/repo/python/ray/serve/generated/__init__.py && \
    cp /workspace/repo/python/ray/serve/generated/serve_pb2.py /workspace/repo/python/serve_pb2.py

RUN printf '%s\n' \
    '#!/bin/bash' \
    'set -e' \
    'mv /workspace/repo/python/ray/serve/tests/conftest.py /workspace/repo/python/ray/serve/tests/conftest.py.bak 2>/dev/null || true' \
    '' \
    'cat > /workspace/repo/python/ray/__init__.py << "PYEOF"' \
    'class ActorHandle: pass' \
    'PYEOF' \
    '' \
    'cat > /workspace/repo/python/ray/actor.py << "PYEOF"' \
    'class ActorHandle: pass' \
    'PYEOF' \
    '' \
    'cat > /workspace/repo/python/ray/serve/__init__.py << "PYEOF"' \
    '# Stubbed out' \
    'PYEOF' \
    '' \
    'cat > /workspace/repo/python/ray/serve/_private/utils.py << "PYEOF"' \
    'from enum import Enum' \
    'class DEFAULT(Enum):' \
    '    VALUE = 1' \
    'PYEOF' \
    '' \
    'cat > /workspace/repo/python/ray/serve/_private/constants.py << "PYEOF"' \
    'SERVE_LOGGER_NAME = "ray.serve"' \
    'PYEOF' \
    '' \
    'cat > /workspace/repo/python/ray/serve/_private/common.py << "PYEOF"' \
    'from dataclasses import dataclass' \
    'from ray.actor import ActorHandle' \
    '' \
    '@dataclass' \
    'class gRPCRequest:' \
    '    grpc_user_request: bytes' \
    '    grpc_proxy_handle: ActorHandle' \
    '' \
    '@dataclass' \
    'class StreamingHTTPRequest:' \
    '    pickled_asgi_scope: bytes' \
    '    http_proxy_handle: ActorHandle' \
    'PYEOF' \
    '' \
    > /workspace/setup_mocks.sh && chmod +x /workspace/setup_mocks.sh

ENV PYTHONPATH=/workspace/repo/python:$PYTHONPATH

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
