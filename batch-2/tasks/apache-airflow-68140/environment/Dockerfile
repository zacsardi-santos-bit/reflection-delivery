FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 2f3865240deda30498c6caef8ce424d6fec427e6 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel flit_core hatchling packaging pathspec pluggy 2>&1 | tail -5

RUN pip install -e airflow-core/ 2>&1 | tail -10

RUN pip install -e task-sdk/ 2>&1 | tail -10

RUN pip install -e devel-common/ --no-deps 2>&1 | tail -5

RUN pip install time-machine requests_mock 2>&1 | tail -5

RUN pip install -e providers/common/compat/ --no-deps 2>&1 | tail -5

RUN pip install -e providers/common/sql/ --no-deps 2>&1 | tail -5

RUN pip install -e providers/http/ --no-deps 2>&1 | tail -5

RUN pip install -e providers/amazon/ 2>&1 | tail -10

RUN pip install "moto[bedrock]>=5.1.2" "pytest-asyncio>=1.1.0" "aiobotocore>=3.0.0" "pytest>=9.0.0" "pytest-mock>=3.14.0" 2>&1 | tail -10

RUN pip install -e task-sdk/ 2>&1 | tail -5

ENV PYTHONPATH=/workspace/repo/task-sdk/src:/workspace/repo/providers/amazon/tests:${PYTHONPATH}


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true
# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` (the client
# verifier exec) keeps go/cargo/java/etc on PATH after /etc/profile resets it.
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
