FROM python:3.12

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 1812de86d6b2969273156725c9f494374ea64a8b && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip uv 2>&1 | tail -5

RUN uv pip install --system \
    -e shared/configuration \
    -e shared/timezones \
    -e shared/serialization \
    -e shared/module_loading \
    -e shared/secrets_masker \
    -e shared/secrets_backend \
    -e shared/logging \
    -e shared/dagnode \
    -e shared/listeners \
    -e shared/observability \
    -e shared/plugins_manager \
    -e shared/providers_discovery \
    -e shared/template_rendering \
    2>&1 | tail -10

RUN uv pip install --system -e task-sdk 2>&1 | tail -10

RUN uv pip install --system -e airflow-core 2>&1 | tail -20

RUN uv pip install --system \
    -e providers/common/compat \
    -e providers/standard \
    -e providers/git \
    2>&1 | tail -10

RUN uv pip install --system -e devel-common 2>&1 | tail -10

RUN uv pip install --system \
    pytest pytest-asyncio pytest-mock time-machine \
    pytest-timeouts pytest-custom-exit-code pytest-instafail \
    pytest-unordered pytest-cov pytest-icdiff requests-mock \
    pytest-rerunfailures \
    2>&1 | tail -10

RUN mkdir -p generated && \
    echo '{}' > generated/provider_dependencies.json && \
    echo '5e041bb908f7d4a4349fa9cebadf37d3e01efca1546a5ec5745495de32bdec44' > generated/provider_dependencies.json.sha256sum

ENV AIRFLOW__DATABASE__SQL_ALCHEMY_CONN=sqlite:////tmp/airflow.db
ENV AIRFLOW__CORE__SQL_ALCHEMY_CONN=sqlite:////tmp/airflow.db
ENV AIRFLOW_HOME=/tmp/airflow
ENV AIRFLOW_SOURCES=/workspace/repo


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
