FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 263549590ecbb0c489fb0166caefa456dfe9b8d4 && \
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

RUN pip install \
    ./airflow-core \
    ./task-sdk \
    . \
    ./providers/common/compat \
    2>&1 | tail -30

RUN pip install -e ./providers/airbyte 2>&1 | tail -10

RUN pip install ./devel-common --no-deps

RUN pip install \
    airbyte-api \
    pytest \
    pytest-asyncio \
    pytest-mock \
    time-machine \
    2>&1 | tail -10

ENV AIRFLOW_SOURCES=/workspace/repo

RUN mkdir -p /workspace/repo/generated && \
    python3 -c "import hashlib, os; root='/workspace/repo'; pyp=root+'/pyproject.toml'; content=open(pyp).read().splitlines(); in_ws=False; files=[]; [ (files.append(root+'/'+l.strip().split('\"')[1]+'/pyproject.toml'), files.append(root+'/'+l.strip().split('\"')[1]+'/provider.yaml')) if l.strip().startswith('\"providers/') else None for l in content ]; h=hashlib.sha256(); [h.update(open(f,'rb').read()) for f in sorted(files) if os.path.exists(f)]; open(root+'/generated/provider_dependencies.json','w').write('{}'); open(root+'/generated/provider_dependencies.json.sha256sum','w').write(h.hexdigest()); print('Hash:', h.hexdigest())"


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
