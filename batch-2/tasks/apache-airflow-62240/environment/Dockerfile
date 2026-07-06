FROM python:3.11

RUN apt-get update && apt-get install -y \
    git build-essential curl ca-certificates patch \
    libssl-dev libffi-dev libkrb5-dev \
    && rm -rf /var/lib/apt/lists/*

RUN pip install uv

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 1ca2797d3bbd0b6480231a9b435323c6f887ba06 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN uv pip install --system hatchling flit_core

RUN uv pip install --system \
    -e task-sdk/ \
    -e airflow-core/ \
    -e providers/common/compat/ \
    -e providers/common/sql/ \
    -e providers/http/ \
    -e providers/amazon/

RUN uv pip install --system --no-deps -e devel-common/

RUN uv pip install --system pytest pytest-mock pytest-asyncio "time-machine>=3.0.0" boto3 botocore


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

# --- offline-harbor: neutralize the pytest_plugin's build-time `uv run` fallback ---
# Even after baking generated/provider_dependencies.json + its .sha256sum, the plugin at
# devel-common/src/tests_common/pytest_plugin.py still calls `uv run` if the hash file is
# missing OR the stored hash doesn't match the recomputed hash. Any drift (e.g. patched
# provider.yaml, minor file rewrites, sort order, or a stale bake) triggers a network
# fetch to pypi at test time, which fails offline. Patch the plugin to replace the two
# `subprocess.check_call(["uv", "run", ...])` invocations with a `pass`, and ensure the
# generated files exist so the branch that reads the hash-file is safe.
RUN PLUGIN=/workspace/repo/devel-common/src/tests_common/pytest_plugin.py; \
    if [ -f "$PLUGIN" ]; then \
      sed -i 's|subprocess\.check_call(\["uv", "run", UPDATE_PROVIDER_DEPENDENCIES_SCRIPT\.as_posix()\])|pass  # patched-offline: uv run disabled|g' "$PLUGIN"; \
      echo "--- verifying pytest_plugin.py patch ---"; \
      if grep -n 'subprocess.check_call(\["uv", "run"' "$PLUGIN"; then \
        echo "ERROR: pytest_plugin.py still contains uv run subprocess call" >&2; exit 1; \
      fi; \
      grep -n 'patched-offline' "$PLUGIN" || true; \
    fi; \
    mkdir -p /workspace/repo/generated; \
    [ -f /workspace/repo/generated/provider_dependencies.json ] || echo '{}' > /workspace/repo/generated/provider_dependencies.json; \
    [ -f /workspace/repo/generated/provider_dependencies.json.sha256sum ] || echo 'baked-offline' > /workspace/repo/generated/provider_dependencies.json.sha256sum