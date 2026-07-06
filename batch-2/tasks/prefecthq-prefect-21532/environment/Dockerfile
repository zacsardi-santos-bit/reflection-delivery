FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential libpq-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/PrefectHQ/prefect.git && \
    git fetch --depth 30 --no-tags origin 02847b688990bd8e4bcdfca3900a41c86a3d0201 && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel

RUN pip install "starlette==0.50.0" "fastapi==0.136.1" 2>&1 | tail -3

RUN pip install -e "." 2>&1 | tail -5

RUN pip install \
    "importlib-metadata>=8.0" \
    "opentelemetry-sdk>=1.27.0,<2.0.0" \
    "opentelemetry-api>=1.27.0,<2.0.0" \
    "opentelemetry-test-utils>=0.48b0" \
    "opentelemetry-distro>=0.48b0" \
    "opentelemetry-instrumentation>=0.48b0" \
    "opentelemetry-instrumentation-logging>=0.48b0" \
    "opentelemetry-instrumentation-system-metrics>=0.48b0" \
    "opentelemetry-exporter-otlp>=1.27.0,<2.0.0" \
    2>&1 | tail -5

RUN pip install \
    "pytest>=9,<10" \
    "pytest-asyncio>=1.1,<1.4" \
    "pytest-cov>=6.0" \
    "pytest-env>=1.1.5" \
    "pytest-timeout>=2.3.1" \
    "pytest-xdist>=3.6.1" \
    "numpy" \
    "asyncpg" \
    "respx>=0.22.0" \
    "fakeredis>=2.0.0" \
    "moto>=5" \
    2>&1 | tail -5

ENV PREFECT_HOME=/tmp/prefect-home


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
