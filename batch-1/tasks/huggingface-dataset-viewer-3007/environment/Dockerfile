FROM python:3.9

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential gcc g++ \
    ffmpeg libavcodec-extra libsndfile1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/huggingface/dataset-viewer.git && \
    git fetch --depth 30 origin 6b11d655f9455deacbb925dbd17f265b883b3e5d && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip 2>&1 | tail -5

RUN pip install \
    "numpy>=1.22.4" \
    "pandas>=2.2.0" \
    "pyarrow==15.0.2" \
    "polars==0.20.15" \
    "pillow>=10.3.0" \
    "tqdm>=4.66.3" \
    "scipy" \
    2>&1 | tail -5

RUN pip install "datasets[audio,vision]==3.4.1" 2>&1 | tail -5

RUN pip install \
    "huggingface-hub==0.28.0" \
    "orjson>=3.9.15" \
    "pytz>=2020.1" \
    "python-dateutil" \
    "librosa" \
    2>&1 | tail -5

RUN pip install \
    "pymongo[srv]==4.6.3" \
    "mongoengine>=0.27.0" \
    "pymongoarrow==1.3.0" \
    "mongo-types==0.15.1" \
    2>&1 | tail -5

RUN pip install \
    "environs>=9.5.0" \
    "networkx>=3.0" \
    "appdirs>=1.4.4" \
    "cryptography>=43.0.1" \
    "psutil>=5.9.4" \
    "starlette-prometheus>=0.9.0" \
    "starlette>=0.37.1" \
    "pydub>=0.25.1" \
    "soundfile>=0.12.1" \
    "aiohttp>=3.10.2" \
    "aiolimiter>=1.0.0" \
    "duckdb>=0.10.3" \
    "h5py>=3.10.0" \
    "mirakuru>=2.4.2" \
    "openpyxl>=3.1.1" \
    "prometheus-client>=0.12.0" \
    "py7zr>=0.20.4" \
    "uvicorn>=0.30.1" \
    "zstandard>=0.22.0" \
    "fsspec[s3]==2024.3.1" \
    "s3fs==2024.3.1" \
    2>&1 | tail -5

RUN python -c "import site; print(site.getsitepackages()[0])" > /tmp/sitepackages.txt && \
    SITE=$(cat /tmp/sitepackages.txt) && \
    mkdir -p "$SITE/presidio_analyzer" && \
    printf 'class AnalyzerEngine:\n    pass\nclass BatchAnalyzerEngine:\n    pass\nclass RecognizerResult:\n    pass\n' \
    > "$SITE/presidio_analyzer/__init__.py"

RUN pip install \
    "pytest>=8.2.2" \
    "pytest-asyncio>=0.23.7" \
    "moto>=4.2.5" \
    "boto3>=1.34.0" \
    "httpx>=0.26.0" \
    "requests" \
    2>&1 | tail -5

RUN pip install --no-deps -e libs/libcommon 2>&1 | tail -5
RUN pip install --no-deps -e services/worker 2>&1 | tail -5

ENV PYTHONPATH=/workspace/repo/libs/libcommon/src:/workspace/repo/services/worker/src
ENV ASSETS_STORAGE_ROOT=/tmp/assets
ENV CACHED_ASSETS_STORAGE_ROOT=/tmp/cached-assets

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
