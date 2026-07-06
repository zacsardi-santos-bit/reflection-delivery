FROM python:3.7

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libxrender1 libsm6 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/deepchem/deepchem.git && \
    git fetch --depth 1 origin 5b1ae0858a5d55760699f2d2a0752aa29a8c8a7a && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel

RUN pip install "tensorflow==1.15.5" "protobuf>=3.8.0,<3.20" "numpy>=1.16,<1.24"

RUN pip install scipy "scikit-learn==0.22.2.post1" pandas "networkx>=2.1" pillow joblib flaky

RUN pip install rdkit

RUN pip install pytest

RUN pip install -e .

WORKDIR /workspace

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true


# --- offline-harbor blobless-shrink: drop blob objects from .git (keep commits+trees, HEAD, working tree) to satisfy .git<100MB while preserving patch-based grading ---
RUN set +e; cd /workspace/repo 2>/dev/null || cd "$(dirname "$(find / -maxdepth 6 -type d -name .git 2>/dev/null | head -1)")"; \
    GIT=/usr/bin/git; command -v "$GIT" >/dev/null 2>&1 || GIT=git; \
    SZ=$("$GIT" count-objects -v 2>/dev/null | awk '/size-pack:/{print $2}'); \
    if [ "${SZ:-0}" -ge 90000 ]; then \
      "$GIT" rev-list --objects --all 2>/dev/null | awk '{print $1}' > /tmp/_bl_names.txt && \
      "$GIT" cat-file --batch-check='%(objectname) %(objecttype)' < /tmp/_bl_names.txt 2>/dev/null | awk '$2=="commit"||$2=="tree"{print $1}' | sort -u > /tmp/_bl_keep.txt && \
      [ -s /tmp/_bl_keep.txt ] && mkdir -p /tmp/_bl_np && \
      "$GIT" pack-objects --no-reuse-delta /tmp/_bl_np/pk < /tmp/_bl_keep.txt && \
      ls /tmp/_bl_np/pk-*.pack >/dev/null 2>&1 && \
      rm -f .git/objects/pack/* && cp /tmp/_bl_np/pk-*.pack /tmp/_bl_np/pk-*.idx .git/objects/pack/ && \
      "$GIT" prune-packed 2>/dev/null; \
      rm -rf /tmp/_bl_names.txt /tmp/_bl_keep.txt /tmp/_bl_np; \
    fi; \
    true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
