FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN pip install uv

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 1e7cc8ed37d52e003893787ac9271bba9b63f4e0 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN uv pip install --system \
    -e "shared/configuration" \
    -e "shared/dagnode" \
    -e "shared/listeners" \
    -e "shared/logging" \
    -e "shared/module_loading" \
    -e "shared/observability" \
    -e "shared/plugins_manager" \
    -e "shared/providers_discovery" \
    -e "shared/secrets_backend" \
    -e "shared/secrets_masker" \
    -e "shared/serialization" \
    -e "shared/state" \
    -e "shared/template_rendering" \
    -e "shared/timezones" \
    -e "task-sdk" \
    -e "airflow-ctl" \
    -e "airflow-core" \
    -e "devel-common" \
    -e "providers/common/compat" \
    -e "providers/common/io" \
    -e "providers/common/sql" \
    -e "providers/smtp" \
    -e "providers/standard" \
    -e "providers/http" \
    -e "providers/amazon" \
    "moto[cloudformation,glue]>=5.1.2" \
    "pytest>=9.0.0" \
    "pytest-mock>=3.14.0" \
    "time-machine>=3.0.0"

ENV AIRFLOW_HOME=/root/airflow


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
