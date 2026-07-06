FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.local/bin:$PATH"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 41a6436a1dd6ac82a1504db145e2958fd426a1fd && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN uv sync --frozen \
    --package apache-airflow-core \
    --package apache-airflow-task-sdk \
    --package apache-airflow-shared-configuration \
    --package apache-airflow-shared-dagnode \
    --package apache-airflow-shared-listeners \
    --package apache-airflow-shared-logging \
    --package apache-airflow-shared-module-loading \
    --package apache-airflow-shared-observability \
    --package apache-airflow-shared-plugins-manager \
    --package apache-airflow-shared-providers-discovery \
    --package apache-airflow-shared-secrets-backend \
    --package apache-airflow-shared-secrets-masker \
    --package apache-airflow-shared-serialization \
    --package apache-airflow-shared-state \
    --package apache-airflow-shared-template-rendering \
    --package apache-airflow-shared-timezones \
    --package apache-airflow-devel-common \
    2>&1 | tail -20

ENV PATH="/workspace/repo/.venv/bin:$PATH"
ENV VIRTUAL_ENV="/workspace/repo/.venv"
ENV _AIRFLOW_SKIP_DB_TESTS=true


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
