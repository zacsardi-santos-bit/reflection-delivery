FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential libssl-dev libffi-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 67014e1056ec3da98d5550552dc96137c85c2b89 && \
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

RUN pip install "hatchling==1.29.0" "flit_core==3.12.0" "GitPython==3.1.47" "gitdb==4.0.12" "smmap==5.0.3" "trove-classifiers==2026.1.14.14" "packaging==26.1" "pathspec==1.1.0" "pluggy==1.6.0"

RUN pip install \
    -e shared/configuration/ \
    -e shared/dagnode/ \
    -e shared/listeners/ \
    -e shared/logging/ \
    -e shared/module_loading/ \
    -e shared/observability/ \
    -e shared/plugins_manager/ \
    -e shared/providers_discovery/ \
    -e shared/secrets_backend/ \
    -e shared/secrets_masker/ \
    -e shared/serialization/ \
    -e shared/state/ \
    -e shared/template_rendering/ \
    -e shared/timezones/

RUN pip install --no-deps -e task-sdk/

RUN pip install \
    -e airflow-core/ \
    -e . \
    -e providers/common/compat/ \
    -e providers/common/sql/ \
    -e providers/standard/ \
    -e providers/openlineage/ \
    -e devel-common/

RUN pip install -e task-sdk/

RUN python3 - <<'PYEOF'
import hashlib
from pathlib import Path

root = Path('/workspace/repo')
pyproject = root / 'pyproject.toml'
content = pyproject.read_text().splitlines()
in_ws = False
files = []
for line in content:
    t = line.strip()
    if not in_ws and t.startswith('[tool.uv.workspace]'):
        in_ws = True
    elif in_ws:
        if t.startswith('#'):
            continue
        if t.startswith('"'):
            path = t.split('"')[1]
            if t.startswith('"providers/'):
                files.append(root / path / 'pyproject.toml')
                files.append(root / path / 'provider.yaml')
        elif t.startswith(']'):
            break

hasher = hashlib.sha256()
for f in sorted(files):
    if f.exists():
        hasher.update(f.read_bytes())

gen = root / 'generated'
gen.mkdir(exist_ok=True)
(gen / 'provider_dependencies.json').write_text('{}')
(gen / 'provider_dependencies.json.sha256sum').write_text(hasher.hexdigest())
print(f'Done. Hash: {hasher.hexdigest()}')
PYEOF

ENV AIRFLOW_HOME=/tmp/airflow
ENV PYTHONPATH=/workspace/repo/providers/openlineage/tests:/workspace/repo/providers/openlineage/src:$PYTHONPATH


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
