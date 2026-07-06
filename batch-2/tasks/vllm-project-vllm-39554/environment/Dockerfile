FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential cmake ninja-build \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/vllm-project/vllm.git && \
    git fetch --depth 1 origin fe5c115ee48229faeb53e4dd66f384d116772d00 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip && pip install "setuptools==77.0.3" wheel packaging jinja2 setuptools-scm cmake ninja

RUN pip install torch --index-url https://download.pytorch.org/whl/cpu

RUN pip install \
    numpy regex cachetools psutil sentencepiece requests tqdm blake3 py-cpuinfo \
    "transformers>=4.56.0" "tokenizers>=0.21.1" "protobuf>=5.29.6" \
    "fastapi[standard]>=0.115.0" "aiohttp>=3.13.3" "openai>=2.0.0" "pydantic>=2.12.0" \
    "prometheus_client>=0.18.0" pillow "tiktoken>=0.6.0" \
    "lm-format-enforcer==0.11.3" "outlines_core==0.2.11" "diskcache==5.6.3" "lark==1.2.2" \
    "typing_extensions>=4.10" "filelock>=3.16.1" partial-json-parser "pyzmq>=25.0.0" msgspec \
    "gguf>=0.17.0" "mistral_common[image]>=1.11.0" \
    soundfile pyyaml huggingface_hub safetensors

ENV VLLM_TARGET_DEVICE=empty

RUN pip install -e . --no-build-isolation

RUN pip install pytest

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
