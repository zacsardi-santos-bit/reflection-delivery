FROM python:3.7-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libglib2.0-0 libsm6 libxext6 libxrender-dev libgomp1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/PaddlePaddle/Paddle.git && \
    git fetch --depth 30 origin 3f371db82a7c720f8f87cb46b79d9c5dc550a663 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN pip install --upgrade pip setuptools wheel

RUN pip install paddlepaddle==1.8.5

RUN pip install "numpy>=1.12" "protobuf<=3.20.0" six pytest

RUN cd /workspace/repo && \
    git config user.email "docker@build.local" && \
    git config user.name "Docker Build" && \
    sed -i 's/self._run_backward(backward_strategy, framework._dygraph_tracer())/self._run_backward(backward_strategy, framework._dygraph_tracer(), False)/' \
        python/paddle/fluid/dygraph/varbase_patch_methods.py && \
    git add python/paddle/fluid/dygraph/varbase_patch_methods.py && \
    git commit -m "fix backward compat with 1.8.5"

RUN SITE=$(python -c "import site; print(site.getsitepackages()[0])") && \
    ln -sf /workspace/repo/python/paddle/fluid/dygraph/varbase_patch_methods.py \
           $SITE/paddle/fluid/dygraph/varbase_patch_methods.py && \
    ln -sf /workspace/repo/python/paddle/fluid/backward.py \
           $SITE/paddle/fluid/backward.py

WORKDIR /workspace

# >>> final-git-hygiene (compliance) >>>
RUN cd /workspace/repo 2>/dev/null && ( \
    B=3f371db82a7c720f8f87cb46b79d9c5dc550a663; H=$(git rev-parse HEAD 2>/dev/null); \
    if [ "$H" != "$B" ]; then git reset --soft "$B" 2>/dev/null || true; fi; \
    git remote remove origin 2>/dev/null || true; \
    git tag -l 2>/dev/null | xargs -r git tag -d 2>/dev/null || true; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    git gc --prune=now 2>/dev/null || true; \
    rm -rf .git/logs ) ; true
# <<< final-git-hygiene (compliance) <<<

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
