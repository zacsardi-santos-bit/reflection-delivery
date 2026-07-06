FROM python:3.14

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libffi-dev libssl-dev libjpeg-dev zlib1g-dev \
    libturbojpeg0-dev libspeex-dev \
    bluez libxml2-utils libudev-dev \
    cargo rustc \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/home-assistant/core.git && \
    git fetch --depth 30 --no-tags origin 249e6c2f3d02656306039d4a32531d90a126f5c6 && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel

RUN pip install uv==0.9.26

RUN uv pip install --system -r requirements.txt

RUN uv pip install --system python-ecobee-api==0.3.2

RUN uv pip install --system \
    pytest==9.0.0 \
    pytest-asyncio==1.3.0 \
    pytest-aiohttp==1.1.0 \
    pytest-socket==0.7.0 \
    pytest-timeout==2.4.0 \
    pytest-cov==7.0.0 \
    pytest-freezer==0.4.9 \
    pytest-sugar==1.0.0 \
    pytest-unordered==0.7.0 \
    pytest-xdist==3.8.0 \
    requests-mock==1.12.1 \
    freezegun==1.5.2 \
    syrupy==5.0.0 \
    respx==0.22.0 \
    mock-open==1.4.0 \
    pydantic==2.12.2 \
    tqdm==4.67.1

RUN uv pip install --system -e . --config-settings editable_mode=compat

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


# offline login-shell PATH bake
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh

# offline-harbor: git blob-drop shrink (client-contract .git<100MB, HEAD==base)
RUN set +e; cd /workspace/repo 2>/dev/null && ( rm -rf .git/modules .git/worktrees 2>/dev/null; git reflog expire --expire=now --all 2>/dev/null; rm -rf .git/logs; git remote remove origin 2>/dev/null; git tag -l | xargs -r git tag -d >/dev/null 2>&1; git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d 2>/dev/null; git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_keep.txt; if [ -s /tmp/_keep.txt ]; then git pack-objects --quiet /tmp/_kp < /tmp/_keep.txt >/dev/null 2>&1 && rm -rf .git/objects/pack && mkdir -p .git/objects/pack && find .git/objects -maxdepth 1 -type d -name '[0-9a-f][0-9a-f]' -exec rm -rf {} + 2>/dev/null; mv /tmp/_kp-*.pack /tmp/_kp-*.idx .git/objects/pack/ 2>/dev/null; fi; git rev-parse HEAD >/dev/null 2>&1 || echo "WARN head-broken"; du -sm .git 2>/dev/null | cut -f1 ) ; true
