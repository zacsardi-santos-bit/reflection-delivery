FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libssl-dev libffi-dev libxml2-dev libxslt-dev \
    && rm -rf /var/lib/apt/lists/*

RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.local/bin:$PATH"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin dc27827de265f9aa49ecb1f4698c521c939df3fb && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN uv sync --frozen --package apache-airflow-core --group dev 2>&1 | tail -30

ENV PATH="/workspace/repo/.venv/bin:$PATH"
ENV AIRFLOW__CORE__UNIT_TEST_MODE=True
ENV AIRFLOW__DATABASE__SQL_ALCHEMY_CONN=sqlite:////tmp/airflow-unit-test.db
ENV AIRFLOW_HOME=/tmp/airflow-home
ENV PYTHONPATH="/workspace/repo/shared/configuration/src:/workspace/repo/shared/dagnode/src:/workspace/repo/shared/listeners/src:/workspace/repo/shared/logging/src:/workspace/repo/shared/module_loading/src:/workspace/repo/shared/observability/src:/workspace/repo/shared/plugins_manager/src:/workspace/repo/shared/providers_discovery/src:/workspace/repo/shared/secrets_backend/src:/workspace/repo/shared/secrets_masker/src:/workspace/repo/shared/serialization/src:/workspace/repo/shared/state/src:/workspace/repo/shared/template_rendering/src:/workspace/repo/shared/timezones/src"


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
