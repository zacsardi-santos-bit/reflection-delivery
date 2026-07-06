FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libssl-dev libffi-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 5dfea277fffd3838c773776132faa77fbf5279af && \
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

RUN pip install \
    -e /workspace/repo/task-sdk \
    -e /workspace/repo/providers/common/compat \
    -e /workspace/repo/providers/common/io \
    -e /workspace/repo/providers/common/sql \
    -e /workspace/repo/providers/smtp \
    -e /workspace/repo/providers/standard \
    -e /workspace/repo/airflow-core

RUN pip install -e "/workspace/repo/devel-common[basic]"

RUN cd /workspace/repo && python scripts/ci/prek/update_providers_dependencies.py

RUN printf '%s\n' \
  'import hashlib' \
  'from pathlib import Path' \
  'root = Path("/workspace/repo")' \
  'lines = (root / "pyproject.toml").read_text().splitlines()' \
  'files = []' \
  'in_ws = False' \
  'for line in lines:' \
  '    t = line.strip()' \
  '    if not in_ws and t.startswith("[tool.uv.workspace]"):' \
  '        in_ws = True' \
  '    elif in_ws:' \
  '        if t.startswith("#"): continue' \
  '        if t.startswith(chr(34)) and t.startswith(chr(34)+"providers/"):' \
  '            p = t.split(chr(34))[1]' \
  '            files.append(root / p / "pyproject.toml")' \
  '            files.append(root / p / "provider.yaml")' \
  '        elif t.startswith("]"): break' \
  'h = hashlib.sha256()' \
  '[h.update(f.read_bytes()) for f in sorted(files)]' \
  '(root / "generated" / "provider_dependencies.json.sha256sum").write_text(h.hexdigest())' \
  'print("sha256sum written")' \
  > /tmp/gen_hash.py && python /tmp/gen_hash.py

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
