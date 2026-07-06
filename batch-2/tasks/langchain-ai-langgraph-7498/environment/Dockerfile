FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential libpq-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/langchain-ai/langgraph.git && \
    git fetch --depth 30 origin 2c98c59fca6c99b696988b97dfaeb885f652920c && \
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

RUN pip install -e libs/checkpoint 2>&1 | tail -5
RUN pip install -e libs/checkpoint-sqlite 2>&1 | tail -5
RUN pip install -e libs/checkpoint-postgres 2>&1 | tail -5
RUN pip install -e libs/prebuilt 2>&1 | tail -5
RUN pip install -e libs/sdk-py 2>&1 | tail -5
RUN pip install -e libs/langgraph 2>&1 | tail -5

RUN pip install \
    pytest pytest-cov pytest-dotenv pytest-mock syrupy httpx \
    "pytest-xdist[psutil]" pytest-repeat \
    "psycopg[binary]" psycopg-pool \
    anyio uvloop==0.22.1 pycryptodome redis \
    2>&1 | tail -10

ENV NO_DOCKER=true
ENV PYTHONPATH=/workspace/repo/libs/langgraph:/workspace/repo


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
