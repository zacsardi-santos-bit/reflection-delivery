FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/vllm-project/vllm.git && \
    git fetch --depth 30 origin 35a49fcfc2295d04fb252c1080f8d7bd9d888e25 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --extra-index-url https://download.pytorch.org/whl/cpu \
    "torch==2.11.0+cpu" 2>&1 | tail -5

RUN pip install --extra-index-url https://download.pytorch.org/whl/cpu \
    -r requirements/common.txt 2>&1 | tail -5

RUN pip install pybase64 pytest pytest-asyncio tblib msgspec huggingface_hub 2>&1 | tail -5

ENV PYTHONPATH=/workspace/repo:$PYTHONPATH
ENV VLLM_TARGET_DEVICE=empty
ENV HF_HUB_OFFLINE=0

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

# offline-bake: hf-cache (1 models: openai-community/gpt2)
ENV HF_HOME=/opt/hf_home
RUN mkdir -p /opt/hf_home && \
    python -c "from huggingface_hub import snapshot_download; snapshot_download('openai-community/gpt2', allow_patterns=['*.json','*.txt','*.model','tokenizer*','*.merges','vocab*','special_tokens*','config*','generation_config*','merges*','preprocessor*','*.tiktoken','*.jinja','*.py'])" 2>/dev/null || true
ENV HF_HUB_OFFLINE=1
ENV TRANSFORMERS_OFFLINE=1

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
