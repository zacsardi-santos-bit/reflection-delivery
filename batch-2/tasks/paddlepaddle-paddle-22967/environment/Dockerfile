FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/PaddlePaddle/Paddle.git && \
    git fetch --depth 30 origin 44b1343aab0478fc9740ab0dfda4f9d2e2e7a572 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

# Install Python dependencies needed by the AST transformation code
RUN pip install --no-cache-dir gast astor six decorator numpy pytest

# Create a setup script outside the git repo that stubs out C-extension-dependent
# paddle init files. This script is run before each test invocation so that even
# if the validation framework resets the git repo the stubs are restored.
RUN cat > /workspace/setup_stubs.sh << 'SETUPEOF'
#!/bin/bash
set -e

# Stub out paddle/__init__.py to prevent real package import chain
cat > /workspace/repo/python/paddle/__init__.py << 'PYEOF'
# Minimal stub - avoids importing C extensions or paddle.dataset
PYEOF

# Stub paddle/fluid/__init__.py with fake layers module and real unique_name
cat > /workspace/repo/python/paddle/fluid/__init__.py << 'PYEOF'
class _FakeObj(object):
    def __getattr__(self, name):
        return _FakeObj()
    def __call__(self, *args, **kwargs):
        return _FakeObj()
    def __iter__(self):
        return iter([])
    def __bool__(self):
        return False
    __nonzero__ = __bool__

class _FakeModule(object):
    def __getattr__(self, name):
        return _FakeObj()

layers = _FakeModule()

from . import unique_name
PYEOF

# Empty out dygraph/__init__.py to avoid C-extension imports
cat > /workspace/repo/python/paddle/fluid/dygraph/__init__.py << 'PYEOF'
# Minimal stub
PYEOF

# Empty out dygraph_to_static/__init__.py to avoid C-extension imports
cat > /workspace/repo/python/paddle/fluid/dygraph/dygraph_to_static/__init__.py << 'PYEOF'
# Minimal stub
PYEOF
SETUPEOF

RUN chmod +x /workspace/setup_stubs.sh

ENV PYTHONPATH=/workspace/repo/python

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
