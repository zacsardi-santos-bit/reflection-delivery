FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential libssl-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 7a8a1c3cb896ee66bb144ad28a2918ac9610492b && \
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
    -e /workspace/repo/airflow-core \
    -e /workspace/repo/task-sdk \
    -e /workspace/repo/providers/common/compat \
    -e /workspace/repo/providers/common/io \
    -e "/workspace/repo/providers/common/sql[datafusion]" \
    -e /workspace/repo/providers/smtp \
    -e /workspace/repo/providers/standard \
    -e "/workspace/repo/providers/common/ai[sql]" \
    -e /workspace/repo/devel-common 2>&1 | tail -30

RUN pip install pytest pytest-asyncio time_machine 2>&1 | tail -10

RUN echo 'import hashlib,json' > /tmp/gen.py \
 && echo 'from pathlib import Path as P' >> /tmp/gen.py \
 && echo 'root=P("/workspace/repo")' >> /tmp/gen.py \
 && echo 'lines=open(str(root/"pyproject.toml")).readlines()' >> /tmp/gen.py \
 && echo 'in_ws=False;files=[]' >> /tmp/gen.py \
 && echo 'for l in lines:' >> /tmp/gen.py \
 && echo '    t=l.strip()' >> /tmp/gen.py \
 && echo '    if not in_ws and "[tool.uv.workspace]" in t:in_ws=True' >> /tmp/gen.py \
 && echo '    elif in_ws:' >> /tmp/gen.py \
 && echo '        if t.startswith("#"):continue' >> /tmp/gen.py \
 && echo '        if t[:1]==chr(34):' >> /tmp/gen.py \
 && echo '            p=t.split(chr(34))[1]' >> /tmp/gen.py \
 && echo '            if p.startswith("providers/"):' >> /tmp/gen.py \
 && echo '                [files.append(root/p/f) for f in ["pyproject.toml","provider.yaml"] if (root/p/f).exists()]' >> /tmp/gen.py \
 && echo '        elif t[:1]=="]":break' >> /tmp/gen.py \
 && echo 'h=hashlib.sha256(b"".join(open(str(f),"rb").read() for f in sorted(files))).hexdigest()' >> /tmp/gen.py \
 && echo 'gen=root/"generated"' >> /tmp/gen.py \
 && echo 'gen.mkdir(exist_ok=True)' >> /tmp/gen.py \
 && echo 'open(str(gen/"provider_dependencies.json"),"w").write(json.dumps({}))' >> /tmp/gen.py \
 && echo 'open(str(gen/"provider_dependencies.json.sha256sum"),"w").write(h)' >> /tmp/gen.py \
 && python3 /tmp/gen.py

ENV PYTHONPATH=/workspace/repo:$PYTHONPATH


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
