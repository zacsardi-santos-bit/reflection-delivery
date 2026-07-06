FROM python:3.8

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential wget \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN wget -q https://repo.anaconda.com/miniconda/Miniconda3-py38_23.1.0-1-Linux-x86_64.sh -O /tmp/miniconda.sh && \
    bash /tmp/miniconda.sh -b -p /opt/conda && \
    rm /tmp/miniconda.sh

ENV PATH="/opt/conda/bin:$PATH"
ENV PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION=python

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ray-project/ray.git && \
    git fetch --depth 30 origin 877076160e555bf3c47e48767654955d0c977b2e && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN pip install https://s3-us-west-2.amazonaws.com/ray-wheels/master/877076160e555bf3c47e48767654955d0c977b2e/ray-2.0.0.dev0-cp38-cp38-manylinux2014_x86_64.whl > /dev/null 2>&1 && \
    pip install "protobuf<4.0" > /dev/null 2>&1

RUN python -c "import ray; import os; print(os.path.dirname(ray.__file__))" > /tmp/ray_dir.txt && \
    RAY_DIR=$(cat /tmp/ray_dir.txt) && \
    cp "$RAY_DIR/_raylet.so" /workspace/repo/python/ray/ && \
    (cp -r "$RAY_DIR/thirdparty_files" /workspace/repo/python/ray/ 2>/dev/null || true) && \
    (cp -r "$RAY_DIR/pickle5_files" /workspace/repo/python/ray/ 2>/dev/null || true) && \
    (cp -r "$RAY_DIR/core/generated" /workspace/repo/python/ray/core/ 2>/dev/null || true) && \
    (cp -r "$RAY_DIR/streaming/generated" /workspace/repo/python/ray/streaming/ 2>/dev/null || true) && \
    ls /workspace/repo/python/ray/_raylet.so

RUN pip install pytest pytest-timeout filelock pydantic > /dev/null 2>&1

ENV PYTHONPATH=/workspace/repo/python:$PYTHONPATH

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
