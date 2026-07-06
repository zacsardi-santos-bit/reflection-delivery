FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 4ed8bdd9ac6fdd4ce95521c5ea243b504326e760 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip wheel setuptools

RUN pip install \
    "hatchling==1.27.0" \
    "flit_core==3.12.0" \
    "GitPython==3.1.45" \
    "gitdb==4.0.12" \
    "packaging==25.0" \
    "pathspec==0.12.1" \
    "pluggy==1.6.0" \
    "smmap==5.0.2" \
    "trove-classifiers"

RUN pip install --no-build-isolation -e airflow-core/ 2>&1 | tail -5

RUN pip install --no-build-isolation -e task-sdk/ 2>&1 | tail -5

RUN pip install --no-build-isolation -e . 2>&1 | tail -5

RUN pip install --no-build-isolation -e providers/common/compat/ 2>&1 | tail -5

RUN pip install --no-build-isolation -e providers/common/sql/ 2>&1 | tail -5

RUN pip install --no-build-isolation -e providers/openlineage/ 2>&1 | tail -5

RUN pip install --no-build-isolation -e providers/standard/ 2>&1 | tail -5

RUN pip install \
    "openlineage-integration-common>=1.41.0" \
    "openlineage-python>=1.41.0" \
    pytest \
    pytest-mock \
    "time-machine[dateutil]>=3.0.0" \
    pyyaml \
    rich

RUN pip install --no-build-isolation -e devel-common/ 2>&1 | tail -5

RUN pip install --no-build-isolation -e task-sdk/ 2>&1 | tail -5

RUN echo '#!/bin/sh' > /usr/local/bin/uv \
 && echo 'exit 0' >> /usr/local/bin/uv \
 && chmod +x /usr/local/bin/uv

RUN echo 'import hashlib' > /tmp/gen.py \
 && echo 'from pathlib import Path' >> /tmp/gen.py \
 && echo 'ROOT=Path("/workspace/repo")' >> /tmp/gen.py \
 && echo 'TOML=ROOT/"pyproject.toml"' >> /tmp/gen.py \
 && echo 'gen=ROOT/"generated"' >> /tmp/gen.py \
 && echo 'gen.mkdir(exist_ok=True)' >> /tmp/gen.py \
 && echo 'lines=TOML.read_text().splitlines()' >> /tmp/gen.py \
 && echo 'in_ws=False' >> /tmp/gen.py \
 && echo 'files=[]' >> /tmp/gen.py \
 && echo 'dq=chr(34)' >> /tmp/gen.py \
 && echo 'for line in lines:' >> /tmp/gen.py \
 && echo '    t=line.strip()' >> /tmp/gen.py \
 && echo '    if not in_ws and t.startswith("[tool.uv.workspace]"):' >> /tmp/gen.py \
 && echo '        in_ws=True' >> /tmp/gen.py \
 && echo '    elif in_ws:' >> /tmp/gen.py \
 && echo '        if t.startswith("#"): continue' >> /tmp/gen.py \
 && echo '        if t.startswith(dq):' >> /tmp/gen.py \
 && echo '            p=t.split(dq)[1]' >> /tmp/gen.py \
 && echo '            if p.startswith("providers/"):' >> /tmp/gen.py \
 && echo '                files.append(ROOT/p/"pyproject.toml")' >> /tmp/gen.py \
 && echo '                files.append(ROOT/p/"provider.yaml")' >> /tmp/gen.py \
 && echo '        elif t.startswith("]"): break' >> /tmp/gen.py \
 && echo 'h=hashlib.sha256()' >> /tmp/gen.py \
 && echo '[h.update(f.read_bytes()) for f in sorted(files) if f.exists()]' >> /tmp/gen.py \
 && echo 'digest=h.hexdigest()' >> /tmp/gen.py \
 && echo '(gen/"provider_dependencies.json").write_text("{}")' >> /tmp/gen.py \
 && echo '(gen/"provider_dependencies.json.sha256sum").write_text(digest)' >> /tmp/gen.py \
 && echo 'print("Created provider_dependencies files, hash:", digest)' >> /tmp/gen.py \
 && python3 /tmp/gen.py

ENV PYTHONPATH=/workspace/repo/task-sdk/src:/workspace/repo/airflow-core/src:/workspace/repo/providers/openlineage/src:/workspace/repo/providers/common/compat/src:/workspace/repo/providers/common/sql/src:/workspace/repo/providers/standard/src:/workspace/repo/devel-common/src
ENV _AIRFLOW_SKIP_DB_TESTS=true
ENV AIRFLOW__CORE__SQL_ALCHEMY_CONN=sqlite:////tmp/airflow.db
ENV AIRFLOW__DATABASE__SQL_ALCHEMY_CONN=sqlite:////tmp/airflow.db
ENV FORCE_LOWEST_DEPENDENCIES=true


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
