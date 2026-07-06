FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 050c35cd3b206a0b412a93b828f59ddd35a7f7fd && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel

RUN pip install -e task-sdk -e airflow-core 2>&1 | tail -5

RUN pip install -e providers/common/compat -e providers/common/sql 2>&1 | tail -5

RUN pip install \
    "google-ads>=26.0.0" \
    "google-auth>=2.29.0" \
    "google-auth-httplib2>=0.0.1" \
    "gcloud-aio-auth>=5.2.0" \
    "google-api-core>=2.11.0,!=2.16.0,!=2.18.0" \
    "google-api-python-client>=2.0.2" \
    "google-cloud-secret-manager" \
    "asgiref>=3.5.2" \
    "tenacity>=8.3.0" \
    2>&1 | tail -5

RUN pip install pytest pytest-mock "time-machine[dateutil]>=3.0.0" pytest-asyncio 2>&1 | tail -5

ENV PYTHONPATH=/workspace/repo/providers/google/src:/workspace/repo/devel-common/src:$PYTHONPATH


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
