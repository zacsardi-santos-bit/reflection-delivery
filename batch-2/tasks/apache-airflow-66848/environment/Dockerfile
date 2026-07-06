FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential libssl-dev libffi-dev \
    && rm -rf /var/lib/apt/lists/*

RUN pip install uv

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 439105f33b3644865c4a58b591ea9c16b4281a7c && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN uv pip install --system "hatchling==1.30.1" "flit_core==3.12.0" "gitpython==3.1.50" "gitdb==4.0.12" "smmap==5.0.3" "packaging==26.2" "pathspec==1.1.1" "pluggy==1.6.0"

RUN uv pip install --system -e /workspace/repo/task-sdk/ -e /workspace/repo/airflow-core/

RUN uv pip install --system --no-deps -e /workspace/repo/devel-common/

RUN uv pip install --system pytest "time-machine[dateutil]" pytest-mock pytest-asyncio pytest-cov filelock rich requests-mock

ENV AIRFLOW_HOME=/root/airflow
ENV AIRFLOW__DATABASE__SQL_ALCHEMY_CONN=sqlite:////root/airflow/airflow.db
ENV AIRFLOW__CORE__UNIT_TEST_MODE=True

RUN mkdir -p /root/airflow


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
