FROM python:3.8

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/PaddlePaddle/Paddle.git && \
    git fetch --depth 30 origin 2bca329566a044b67783c2349584eb8036b6fee1 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN pip install 'numpy==1.23.5' 'protobuf>=3.1.0,<4.0.0' 'gast==0.3.3'

RUN pip install paddlepaddle==1.8.5

RUN python -c "import paddle; print('paddle import ok:', paddle.__file__)"

RUN PADDLE_SITE=$(python -c "import site, os; dirs=[os.path.join(d,'paddle') for d in site.getsitepackages() if os.path.isdir(os.path.join(d,'paddle'))]; print(dirs[0])") && \
    echo "PADDLE_SITE=$PADDLE_SITE" && \
    ln -sf /workspace/repo/python/paddle/fluid/contrib/mixed_precision/fp16_utils.py \
        $PADDLE_SITE/fluid/contrib/mixed_precision/fp16_utils.py && \
    ln -sf /workspace/repo/python/paddle/fluid/incubate/fleet/collective/__init__.py \
        $PADDLE_SITE/fluid/incubate/fleet/collective/__init__.py && \
    ln -sf /workspace/repo/python/paddle/fluid/backward.py \
        $PADDLE_SITE/fluid/backward.py && \
    ln -sf /workspace/repo/python/paddle/fluid/optimizer.py \
        $PADDLE_SITE/fluid/optimizer.py

RUN mkdir -p /workspace/tests && \
    ln -sf /workspace/repo/python/paddle/fluid/contrib/tests/test_fp16_utils.py \
        /workspace/tests/test_fp16_utils.py && \
    ln -sf /workspace/repo/python/paddle/fluid/tests/unittests/test_collective_optimizer.py \
        /workspace/tests/test_collective_optimizer.py

RUN pip install pytest

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
