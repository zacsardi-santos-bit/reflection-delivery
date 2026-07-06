FROM python:3.8

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    iptables libcap-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/Yelp/paasta.git && \
    git fetch --depth 30 origin ad990e03cb5bdf5fd54a4f95e52163489a263082 && \
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

RUN pip install --quiet \
    "a-sync>=0.5.0" \
    "aiohttp>=3.2.1" \
    "argcomplete>=0.8.1" \
    boto3 \
    botocore \
    "bravado>=8.4.0" \
    "choice>=0.1" \
    "chronos-python>=1.2.0" \
    "cookiecutter>=1.4.0" \
    croniter \
    docker-py \
    "dulwich>=0.17.3" \
    "ephemeral-port-reserve>=1.0.1" \
    gevent \
    graphviz \
    gunicorn \
    "humanize>=0.5.1" \
    isodate \
    "jsonschema[format]" \
    "kazoo>=2.0.0" \
    "kubernetes==9.0.1" \
    "marathon>=0.9.3" \
    "mypy-extensions>=0.3.0" \
    objgraph \
    "progressbar2>=3.10.0" \
    "pyramid>=1.8" \
    "pyramid-swagger>=2.3.0" \
    "pysensu-yelp>=0.3.4" \
    PyStaticConfiguration \
    "python-crontab>=2.1.1" \
    "python-dateutil>=2.4.0" \
    "pytimeparse>=1.1.0" \
    "pytz>=2014.10" \
    "requests>=2.18.4" \
    requests-cache \
    retry \
    "ruamel.yaml" \
    "sensu-plugin" \
    "service-configuration-lib>=0.12.0" \
    "slackclient>=1.2.1,<2.0" \
    typing-extensions \
    tzlocal \
    "ujson>=1.35" \
    wsgicors

RUN pip install --quiet \
    mock \
    pytest \
    "pytest-asyncio" \
    asynctest \
    freezegun

RUN pip install -e . --no-deps

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
