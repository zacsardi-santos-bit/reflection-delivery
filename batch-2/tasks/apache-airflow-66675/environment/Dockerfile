FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential libsqlite3-dev \
    && rm -rf /var/lib/apt/lists/*

RUN pip install uv

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 3e81987653217c0ba3ca895194377a6e9aa1de3c && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN uv sync --package apache-airflow-core

ENV VIRTUAL_ENV=/workspace/repo/.venv
ENV PATH="/workspace/repo/.venv/bin:$PATH"

RUN pip install --no-deps -e /workspace/repo/devel-common

RUN pip install \
    "time-machine>=3.0.0" \
    "pytest>=7.0" \
    "pytest-mock>=3.14.0" \
    "pytest-asyncio>=1.1.0" \
    "kgb>=7.2.0" \
    "aioresponses>=0.7.6" \
    "requests-mock>=1.11.0" \
    "filelock>=3.13.0" \
    "jmespath>=0.7.0" \
    "semver>=3.0.2" \
    "coverage>=7.4.0" \
    "sqlalchemy-utils>=0.41.2" \
    "pytest-cov>=4.1.0" \
    "pytest-instafail>=0.5.0" \
    "pytest-rerunfailures>=13.0"

ENV AIRFLOW_HOME=/tmp/airflow
ENV AIRFLOW__DATABASE__SQL_ALCHEMY_CONN=sqlite:////tmp/airflow/airflow.db
ENV PYTHONPATH=/workspace/repo/airflow-core/tests


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
