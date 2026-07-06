FROM python:3.8

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libssl-dev libffi-dev libjpeg-dev zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/home-assistant/core.git && \
    git fetch --depth 30 origin 9368891b1baef0e38be2a8aa9436c1dc2623bde3 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel

RUN pip install \
    aiohttp==3.7.4.post0 \
    astral==2.2 \
    async_timeout==3.0.1 \
    attrs==20.3.0 \
    awesomeversion==21.2.3 \
    bcrypt==3.1.7 \
    "certifi>=2020.12.5" \
    ciso8601==2.1.3 \
    httpx==0.17.1 \
    "jinja2>=3.0,<3.1" \
    "PyJWT==1.7.1" \
    "cryptography==3.3.2" \
    python-slugify==4.0.1 \
    "pytz>=2021.1" \
    pyyaml==5.4.1 \
    requests==2.25.1 \
    "ruamel.yaml==0.15.100" \
    voluptuous==0.12.1 \
    voluptuous-serialize==2.4.0 \
    yarl==1.6.3

RUN pip install \
    sqlalchemy==1.3.23 \
    "paho-mqtt==1.5.1" \
    "aiohttp_cors==0.7.0" \
    multidict \
    aiodiscover==1.3.3 \
    "zeroconf==0.29.0" \
    defusedxml==0.6.0 \
    distro==1.5.0 \
    emoji==1.2.0

RUN pip install \
    pytest==6.2.3 \
    "pytest-aiohttp==0.3.0" \
    pytest-asyncio \
    "pytest-timeout==1.4.2" \
    "requests_mock==1.8.0" \
    "responses==0.12.0" \
    "respx==0.16.2" \
    "pytest-xdist==2.1.0"

RUN pip install -e .

ENV PYTHONPATH=/workspace/repo:$PYTHONPATH

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
