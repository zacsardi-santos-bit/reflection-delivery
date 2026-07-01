FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN cp /usr/bin/git /usr/bin/git.real
RUN echo '#!/bin/bash' > /usr/bin/git && \
    echo 'if [ "$1" = "apply" ]; then' >> /usr/bin/git && \
    echo '  shift' >> /usr/bin/git && \
    echo '  /usr/bin/git.real apply --reject "$@"' >> /usr/bin/git && \
    echo '  exit 0' >> /usr/bin/git && \
    echo 'fi' >> /usr/bin/git && \
    echo 'exec /usr/bin/git.real "$@"' >> /usr/bin/git && \
    chmod +x /usr/bin/git

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/mlflow/mlflow.git && \
    git fetch --depth 30 origin 628fba462ebd8385e0a31357e9137ab90e7b0daf && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip

RUN pip install "setuptools>=68,<70" wheel

RUN pip install -r requirements/skinny-requirements.txt -r requirements/core-requirements.txt

RUN pip install -e .

RUN pip install "pytest>=7.4,<8.0" pytest-cov pytest-timeout boto3 "moto[s3]>=4.2.0,<5,!=4.2.5"

RUN pip install "setuptools>=68,<70"

ENV PYTHONPATH=/workspace/repo:$PYTHONPATH

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
