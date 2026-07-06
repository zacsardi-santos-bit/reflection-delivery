FROM python:3.14

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libjpeg-dev libpng-dev libffi-dev libssl-dev \
    libxml2-dev libxslt-dev zlib1g-dev libwebp-dev \
    libfreetype6-dev libtiff-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/home-assistant/core.git && \
    git fetch --depth 30 --no-tags origin 6cb63a60bc99d79bb647f27fd42618d3c00800a2 && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
WORKDIR /workspace/repo

RUN pip install --no-cache-dir uv

RUN uv pip install --system --no-cache \
    pytest==9.0.0 \
    pytest-asyncio==1.3.0 \
    pytest-aiohttp==1.1.0 \
    pytest-socket==0.7.0 \
    pytest-freezer==0.4.9 \
    pytest-timeout==2.4.0 \
    pytest-unordered==0.7.0 \
    freezegun==1.5.2 \
    syrupy==5.0.0 \
    requests-mock==1.12.1 \
    respx==0.22.0 \
    2>&1 | tail -5

RUN uv pip install --system --no-cache -e . 2>&1 | tail -5

RUN uv pip install --system --no-cache \
    HueBLE==2.1.0 \
    habluetooth==5.8.0 \
    bleak==2.1.1 \
    bleak-retry-connector==4.4.3 \
    bluetooth-auto-recovery==1.5.3 \
    bluetooth-data-tools==1.28.4 \
    bluetooth-adapters==2.1.0 \
    aiousbwatcher==1.1.1 \
    pyserial==3.5 \
    2>&1 | tail -5

ENV PYTHONPATH=/workspace/repo


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
