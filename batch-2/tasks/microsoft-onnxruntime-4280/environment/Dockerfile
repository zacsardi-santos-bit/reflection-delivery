FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/microsoft/onnxruntime.git && \
    git fetch --depth 1 origin c7ffa056b47d626328202396b8e9ef41803b6185 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel
RUN pip install pytest numpy cerberus torch

RUN mkdir -p /workspace/onnxmock/onnxruntime/capi && \
    touch /workspace/onnxmock/onnxruntime/__init__.py && \
    touch /workspace/onnxmock/onnxruntime/capi/__init__.py && \
    printf '%s\n' \
    'class TrainingParameters: pass' \
    'class TrainingSession: pass' \
    'class RunOptions: pass' \
    'class SessionOptions: pass' \
    'class NodeArg: pass' \
    'class ModelMetadata: pass' \
    'class GraphOptimizationLevel: pass' \
    'class ExecutionMode: pass' \
    'class OrtDevice: pass' \
    'class SessionIOBinding: pass' \
    'class Session: pass' \
    'class InferenceSession: pass' \
    'class IOBinding: pass' \
    'def get_all_providers(): return []' \
    'def get_available_providers(): return []' \
    'def get_device(): return "CPU"' \
    'def set_seed(s): pass' \
    'def set_default_logger_severity(s): pass' \
    'def check_distro_info(): pass' \
    > /workspace/onnxmock/onnxruntime/capi/_pybind_state.py && \
    printf '%s\n' \
    'class Session: pass' \
    'class InferenceSession: pass' \
    'class IOBinding: pass' \
    > /workspace/onnxmock/onnxruntime/capi/session.py && \
    printf '%s\n' \
    'def check_distro_info(): pass' \
    > /workspace/onnxmock/onnxruntime/capi/onnxruntime_validation.py && \
    ln -sf /workspace/repo/orttraining/orttraining/python/training /workspace/onnxmock/onnxruntime/capi/training

ENV PYTHONPATH=/workspace/onnxmock:/workspace/repo:$PYTHONPATH

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

# --- offline-size: keep .git < 100MB by dropping test-irrelevant blob objects (docs/ tree
#     and any blob >=1MB, i.e. large media/binaries) from the shallow single-commit pack.
#     Commit + all trees + source/test blobs are retained; working-tree files stay on disk;
#     HEAD stays == BASE; repo stays shallow. Uses ls-tree (reliable) not rev-list --objects. ---
RUN cd /workspace/repo && \
    if [ -d .git ] && [ "$(du -sm .git 2>/dev/null | cut -f1)" -ge 100 ]; then \
      git ls-tree -r -l HEAD 2>/dev/null \
        | awk '$2=="blob" && ($5 ~ /^docs\// || $4+0>=1048576){print $3}' | sort -u > /tmp/_drop.txt && \
      git cat-file --batch-all-objects --batch-check='%(objectname)' 2>/dev/null | sort -u > /tmp/_all.txt && \
      comm -23 /tmp/_all.txt /tmp/_drop.txt > /tmp/_keep.txt && \
      rm -rf /tmp/_np && mkdir -p /tmp/_np && \
      git pack-objects /tmp/_np/pack < /tmp/_keep.txt >/dev/null 2>&1 && \
      rm -f .git/objects/pack/*.pack .git/objects/pack/*.idx .git/objects/pack/*.rev && \
      cp /tmp/_np/pack-*.pack /tmp/_np/pack-*.idx .git/objects/pack/ && \
      (git reflog expire --expire=now --all 2>/dev/null || true) && \
      rm -rf .git/logs /tmp/_drop.txt /tmp/_all.txt /tmp/_keep.txt /tmp/_np ; \
    fi

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
