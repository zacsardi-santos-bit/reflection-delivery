FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libcairo2-dev libgirepository1.0-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/PrefectHQ/prefect.git && \
    git fetch --depth 30 origin 3eaf465527e46cbc98618acf1957e7f6584625ae && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel 2>&1 | tail -5

RUN echo "path_separator = os" >> /workspace/repo/src/prefect/server/database/alembic.ini

RUN pip install "python-multipart>=0.0.7,<0.0.9" "pydantic[email]>=2.5.0,<2.7.0" 2>&1 | tail -5
RUN pip install -r requirements-client.txt 2>&1 | tail -20

RUN pip install \
    aiosqlite \
    "alembic>=1.7.5,<1.9.0" \
    "asyncpg>=0.23" \
    "click>=8.0,<8.2" \
    "cryptography>=36.0.1" \
    "dateparser>=1.1.1,<2.0.0" \
    "docker>=4.0,<7.0" \
    "graphviz>=0.20.1" \
    "griffe>=0.20.0,<0.47.0" \
    "jinja2>=3.0.0,<4.0.0" \
    "humanize>=4.9.0" \
    "pytz>=2021.1,<2025" \
    "readchar>=4.0.0,<5.0.0" \
    "sqlalchemy[asyncio]>=1.4.22,!=1.4.33,<3.0.0" \
    "typer>=0.12.0,!=0.12.2,<0.13.0" \
    2>&1 | tail -20

RUN pip install \
    "pytest>7,<8" \
    "pytest-asyncio>=0.18.2,!=0.22.0,<0.23.0" \
    pytest-env \
    pytest-timeout \
    "pytest-xdist<3.4.0" \
    "pluggy>=1.4.0" \
    respx \
    moto \
    mypy \
    mock \
    pyyaml \
    requests \
    virtualenv \
    watchfiles \
    2>&1 | tail -20

RUN pip install -e . 2>&1 | tail -20

ENV PYTHONPATH=/workspace/repo/src:$PYTHONPATH

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
