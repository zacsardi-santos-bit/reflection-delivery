FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential libssl-dev libffi-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 26ead2106741c5b36a3d22ca894408f4aaa4bfc0 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install uv flit_core

RUN pip install --no-deps -e ./task-sdk

RUN pip install --no-deps \
    -e ./providers/common/compat \
    -e ./providers/common/io \
    -e ./providers/common/sql \
    -e ./providers/smtp \
    -e ./providers/standard \
    -e ./providers/git

RUN pip install --no-deps -e .

RUN pip install --no-deps -e ./devel-common

RUN pip install -e ./airflow-core 2>&1 | tail -30

RUN pip install pytest pytest-asyncio pytest-mock time-machine httpx \
    pytest-custom-exit-code pytest-timeouts pytest-unordered pytest-instafail 2>&1 | tail -5

RUN pip install GitPython aiosmtplib pyyaml rich 2>&1 | tail -5

RUN cd /workspace/repo/scripts/ci/prek && python update_providers_dependencies.py 2>&1 | tail -20

RUN python3 -c "import hashlib; from pathlib import Path; root=Path('/workspace/repo'); txt=open(str(root/'pyproject.toml')).read().splitlines(); start=next((i for i,l in enumerate(txt) if '[tool.uv.workspace]' in l), None); items=txt[start:] if start is not None else []; members=[l.strip().strip('\"') for l in items if l.strip().startswith('\"providers/')]; files=[f for m in members for f in [root/m/'pyproject.toml', root/m/'provider.yaml'] if (root/m/'pyproject.toml').exists()]; h=hashlib.sha256(); [h.update(f.read_bytes()) for f in sorted(files)]; open(str(root/'generated'/'provider_dependencies.json.sha256sum'),'w').write(h.hexdigest()); print('Hash saved')"

ENV PYTHONPATH=/workspace/repo/devel-common/src:$PYTHONPATH


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

# --- offline-harbor: make the airflow test plugin regenerate provider_dependencies.json OFFLINE.
# devel-common/src/tests_common/pytest_plugin.py runs `uv run update_providers_dependencies.py` at
# pytest collection whenever generated/provider_dependencies.json is stale; `uv run` auto-downloads
# a standalone CPython from github -> fails offline ("Temporary failure in name resolution"). This
# task's golden patch (oracle) rewrites many providers/*/{pyproject.toml,provider.yaml}, so any
# pre-baked hash no longer matches and the `uv run` fires anyway. Fix: patch the plugin to invoke
# the generator with the already-installed interpreter (sys.executable) instead of `uv run` — the
# script only reads local files (deps pyyaml/rich already installed), so it works fully offline and
# regenerates the JSON correctly regardless of which provider files changed.
RUN python3 - <<'PYEOF'
import pathlib
p = pathlib.Path("/workspace/repo/devel-common/src/tests_common/pytest_plugin.py")
src = p.read_text()
needle = 'subprocess.check_call(["uv", "run", UPDATE_PROVIDER_DEPENDENCIES_SCRIPT.as_posix()])'
repl = 'subprocess.check_call([sys.executable, UPDATE_PROVIDER_DEPENDENCIES_SCRIPT.as_posix()])'
assert needle in src, "uv run invocation not found in pytest_plugin.py"
src = src.replace(needle, repl)
if "import sys" not in src:
    src = src.replace("import subprocess", "import subprocess\nimport sys", 1)
p.write_text(src)
print("patched pytest_plugin.py: uv run -> sys.executable")
PYEOF

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
