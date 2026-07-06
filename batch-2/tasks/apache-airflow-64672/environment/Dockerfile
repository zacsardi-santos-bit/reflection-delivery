FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential libpq-dev \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip uv

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 3b6ee39e9e919a7e8325bce212fee4f5a66d3476 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install pyyaml rich 2>&1 | tail -5

RUN cd /workspace/repo/scripts/ci/prek && python update_providers_dependencies.py 2>&1 | tail -20

RUN uv pip install --system \
    -e /workspace/repo/task-sdk \
    -e /workspace/repo/airflow-core \
    -e /workspace/repo/providers/common/compat \
    -e /workspace/repo/providers/common/sql \
    -e "/workspace/repo/devel-common[basic]" \
    -e /workspace/repo/providers/google \
    werkzeug \
    2>&1 | tail -30

ENV AIRFLOW_SOURCES=/workspace/repo
ENV AIRFLOW__CORE__UNIT_TEST_MODE=True


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

# --- offline-harbor: bake airflow provider_dependencies so tests need no network ---
# The tests_common pytest plugin runs `uv run scripts/ci/prek/update_providers_dependencies.py`
# at collection time unless generated/provider_dependencies.json AND its .sha256sum exist with a
# matching hash. That `uv run` downloads a standalone CPython 3.10 from GitHub (PEP-723
# requires-python >=3.10,<3.11), which fails when allow_internet=false. Generate the JSON now
# (network available) + write its matching sha256sum, and pre-cache py3.10 as a fallback.
COPY bake_offline_deps.py /tmp/bake_offline_deps.py
RUN command -v uv >/dev/null 2>&1 || pip install uv || python -m pip install uv || true; \
    cd /workspace/repo; \
    uv python install 3.10 || true; \
    if [ -f scripts/ci/prek/update_providers_dependencies.py ]; then \
      uv run scripts/ci/prek/update_providers_dependencies.py \
        || uv run --python 3.10 scripts/ci/prek/update_providers_dependencies.py \
        || true; \
    fi; \
    python3 /tmp/bake_offline_deps.py || true
# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` (the client
# verifier exec) keeps go/cargo/java/etc on PATH after /etc/profile resets it.
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
