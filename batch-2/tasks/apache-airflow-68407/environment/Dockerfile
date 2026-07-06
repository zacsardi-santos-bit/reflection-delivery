FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin f6bd8b48d296f95d44f819714514cfdb0c967c44 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip

RUN pip install uv

RUN uv python install 3.10

RUN pip install hatchling flit_core packaging GitPython

RUN pip install --no-deps \
    -e airflow-core \
    -e task-sdk \
    -e providers/common/compat \
    -e providers/common/sql \
    -e providers/standard \
    -e providers/common/ai \
    -e devel-common

RUN pip install \
    "a2wsgi>=1.10.8" \
    "cachetools>=6.0.0" \
    "aiosqlite>=0.20.0,<0.22.0" \
    "alembic>=1.13.1,<2.0" \
    "argcomplete>=1.10" \
    "asgiref>=2.3.0" \
    "attrs>=22.1.0" \
    "babel>=2.17.0" \
    "cadwyn>=6.1.1" \
    "colorlog>=6.8.2" \
    "cron-descriptor>=1.2.24" \
    "croniter>=2.0.2" \
    "cryptography>=44.0.3" \
    "deprecated>=1.2.13" \
    "dill>=0.2.2" \
    "fastapi>=0.129.0,<0.137.0" \
    "uvicorn>=0.37.0" \
    "starlette>=1.0.1" \
    "fsspec>=2023.10.0" \
    "greenback>=1.2.1" \
    "httpx>=0.27.0" \
    "importlib_metadata>=6.5" \
    "isoduration>=20.11.0" \
    "itsdangerous>=2.0" \
    "jinja2>=3.1.5" \
    "jsonschema>=4.19.1" \
    "lazy-object-proxy>=1.2.0" \
    "linkify-it-py>=2.0.0" \
    "lockfile>=0.12.2" \
    "methodtools>=0.4.7" \
    "msgspec>=0.19.0" \
    "natsort>=8.4.0" \
    "opentelemetry-api>=1.27.0" \
    "opentelemetry-exporter-otlp>=1.27.0" \
    "opentelemetry-proto>=1.27.0" \
    "packaging>=25.0" \
    "pathspec>=0.9.0" \
    "pendulum>=3.1.0" \
    "pluggy>=1.5.0" \
    "psutil>=6.1.0" \
    "pydantic>=2.11.0" \
    "pydantic-ai-slim" \
    "pygments>=2.0.1" \
    "pyjwt>=2.11.0" \
    "pygtrie>=2.5.0" \
    "python-daemon>=3.0.0" \
    "python-dateutil>=2.7.0" \
    "python-slugify>=5.0" \
    "requests>=2.32.0,<3" \
    "rich>=13.6.0" \
    "rich-argparse>=1.0.0" \
    "setproctitle>=1.3.3" \
    "sqlalchemy[asyncio]>=2.0.48" \
    "sqlglot>=30.0.0" \
    "structlog>=25.4.0" \
    "svcs>=25.1.0" \
    "tabulate>=0.9.0" \
    "tenacity>=8.3.0" \
    "termcolor>=3.0.0" \
    "time-machine[dateutil]>=3.0.0" \
    "typing-extensions>=4.14.1" \
    "universal-pathlib>=0.3.8" \
    "uuid6>=2024.7.10" \
    "filelock>=3.13.0" \
    "jmespath>=0.7.0" \
    "semver>=3.0.2" \
    "pyyaml>=6.0.3" \
    "libcst>=1.8.2" \
    "sqlparse" \
    "more-itertools" \
    pytest pytest-asyncio pytest-mock

ENV PYTHONPATH=/workspace/repo/airflow-core/src:/workspace/repo/task-sdk/src:/workspace/repo/providers/common/ai/src:/workspace/repo/providers/common/compat/src:/workspace/repo/providers/common/sql/src:/workspace/repo/providers/standard/src:/workspace/repo/devel-common/src

# --- offline-harbor: make the airflow test plugin regenerate provider_dependencies.json OFFLINE.
# devel-common/src/tests_common/pytest_plugin.py runs `uv run update_providers_dependencies.py` at
# pytest collection; `uv run` auto-downloads a standalone CPython from github -> fails offline
# ("Temporary failure in name resolution"). Fix: patch the plugin to invoke the generator with the
# already-installed interpreter (sys.executable) instead of `uv run` — the script only reads local
# files (pyyaml/rich already installed), so it works fully offline.
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
