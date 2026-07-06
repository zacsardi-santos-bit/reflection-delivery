FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/aws/sagemaker-python-sdk.git && \
    git fetch --depth 1 origin a6327959c489bd4cca2f68b536c2a6d479a4e9f6 && \
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

RUN pip install \
    "attrs>=23.1.0,<24" \
    "boto3>=1.33.3,<2.0" \
    "cloudpickle==2.2.1" \
    "google-pasta" \
    "numpy>=1.9.0,<2.0" \
    "protobuf>=3.12,<5.0" \
    "smdebug_rulesconfig==1.0.1" \
    "importlib-metadata>=1.4.0,<7.0" \
    "packaging>=20.0" \
    "pandas>=1.3.5,<2.0" \
    "pathos" \
    "schema" \
    "PyYAML~=6.0" \
    "jsonschema" \
    "platformdirs" \
    "tblib>=1.7.0,<4" \
    "urllib3>=1.26.8,<3.0.0" \
    "requests" \
    "docker" \
    "tqdm" \
    "psutil" \
    2>&1 | tail -5

RUN pip install -e . 2>&1 | tail -5

RUN pip install \
    "pytest==6.2.5" \
    "mock==4.0.3" \
    "contextlib2==21.6.0" \
    2>&1 | tail -5

ENV PYTHONPATH=/workspace/repo/src:/workspace/repo:$PYTHONPATH

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH

# dv01-blobdrop: shrink .git<100MB by dropping blob objects (working tree kept; grade uses patch -p1)
RUN cd /workspace/repo && \
    git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_nb.txt && \
    ( cat /tmp/_nb.txt | git pack-objects --non-empty /tmp/_gcpk ) && \
    rm -f .git/objects/pack/* && cp /tmp/_gcpk-*.pack /tmp/_gcpk-*.idx .git/objects/pack/ && \
    rm -f /tmp/_nb.txt /tmp/_gcpk-* && \
    (git reflog expire --expire=now --all 2>/dev/null || true) && rm -rf .git/logs && \
    (git remote remove origin 2>/dev/null || true) && (git tag -l | xargs -r git tag -d 2>/dev/null || true) && \
    (git gc --prune=now 2>/dev/null || true)

RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
