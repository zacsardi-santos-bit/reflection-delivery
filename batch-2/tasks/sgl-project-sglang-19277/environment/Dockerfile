FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/sgl-project/sglang.git && \
    git fetch --depth 30 --no-tags origin 02ca107b2c072d6ef6dde6e76b5ac1d33306ad76 && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
RUN pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cpu 2>&1 | tail -5

RUN pip install --no-cache-dir pydantic numpy pybase64 requests ipython tqdm aiohttp pytest

RUN printf '%s\n' \
    'import os' \
    'p="/workspace/repo/python/sglang/srt/debug_utils/comparator/unshard/planner.py"' \
    'if os.path.exists(p):' \
    '    s=open(p).read()' \
    '    s=s.replace("if axis_name not in pinfo:","if axis_name not in pinfo and axis_name.value not in pinfo:")' \
    '    s=s.replace("ainfo = pinfo[axis_name]","ainfo = pinfo[axis_name if axis_name in pinfo else axis_name.value]")' \
    '    open(p,"w").write(s)' \
    > /usr/local/bin/fixup_planner.py

ENV PYTHONPATH=/workspace/repo/python


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


# offline login-shell PATH bake
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh

# offline-harbor: git blob-drop shrink (client-contract .git<100MB, HEAD==base)
RUN set +e; cd /workspace/repo 2>/dev/null && ( rm -rf .git/modules .git/worktrees 2>/dev/null; git reflog expire --expire=now --all 2>/dev/null; rm -rf .git/logs; git remote remove origin 2>/dev/null; git tag -l | xargs -r git tag -d >/dev/null 2>&1; git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d 2>/dev/null; git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_keep.txt; if [ -s /tmp/_keep.txt ]; then git pack-objects --quiet /tmp/_kp < /tmp/_keep.txt >/dev/null 2>&1 && rm -rf .git/objects/pack && mkdir -p .git/objects/pack && find .git/objects -maxdepth 1 -type d -name '[0-9a-f][0-9a-f]' -exec rm -rf {} + 2>/dev/null; mv /tmp/_kp-*.pack /tmp/_kp-*.idx .git/objects/pack/ 2>/dev/null; fi; git rev-parse HEAD >/dev/null 2>&1 || echo "WARN head-broken"; du -sm .git 2>/dev/null | cut -f1 ) ; true
