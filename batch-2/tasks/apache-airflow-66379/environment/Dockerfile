FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN pip install uv

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 2a92467755718f29d6ef3464323bab41309eab1b && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

ENV UV_SYSTEM_PYTHON=1

RUN uv pip install --system \
    -e ./shared/configuration \
    -e ./shared/dagnode \
    -e ./shared/listeners \
    -e ./shared/logging \
    -e ./shared/module_loading \
    -e ./shared/observability \
    -e ./shared/plugins_manager \
    -e ./shared/providers_discovery \
    -e ./shared/secrets_backend \
    -e ./shared/secrets_masker \
    -e ./shared/serialization \
    -e ./shared/state \
    -e ./shared/timezones \
    -e ./shared/template_rendering

RUN uv pip install --system \
    -e ./airflow-core \
    -e ./task-sdk

RUN uv pip install --system \
    -e ./providers/common/compat \
    -e ./providers/common/sql \
    -e ./providers/elasticsearch \
    -e ./providers/opensearch

RUN uv pip install --system --no-deps -e ./devel-common

RUN uv pip install --system \
    pytest \
    "pytest-mock>=3.14.0" \
    "time-machine[dateutil]" \
    pytest-asyncio \
    structlog

ENV AIRFLOW_HOME=/tmp/airflow
ENV _AIRFLOW_SKIP_DB_TESTS=true
ENV _AIRFLOW__AS_LIBRARY=true

RUN mkdir -p /tmp/airflow


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
