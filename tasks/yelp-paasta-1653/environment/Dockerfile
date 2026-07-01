FROM python:3.6

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/Yelp/paasta.git && \
    git fetch --depth 30 origin 1329fdb025fc8301d70f3b67433c8036327e3dfb && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade "pip==21.3.1" "setuptools<58" wheel

RUN pip install \
    "asynctest==0.11.1" \
    "mock==2.0.0" \
    "pytest==3.3.1" \
    "pytest-asyncio==0.8.0" \
    "attrs==17.4.0" \
    "freezegun==0.3.7" \
    "aiohttp==2.3.3" \
    "mypy_extensions" \
    "typing_extensions==3.6.2.1" \
    "marathon==0.9.3" \
    "requests==2.6.2" \
    "requests-cache==0.4.13" \
    "docker-py==1.2.3" \
    "kazoo==2.2" \
    "humanize==0.5.1" \
    "isodate==0.5.1" \
    "pytimeparse==1.1.5" \
    "pytz==2016.10" \
    "PyYAML==3.11" \
    "retry==0.9.2" \
    "ujson==1.35" \
    "simplejson==3.10.0" \
    "ruamel.yaml==0.15.23" \
    "service-configuration-lib==0.12.0" \
    "dulwich==0.17.3" \
    "argcomplete==0.8.1" \
    "ephemeral-port-reserve==1.1.0" \
    "progressbar2==3.10.1" \
    "choice==0.1" \
    "boto3==1.3.0" \
    "addict==2.1.0" \
    "pysensu-yelp==0.3.4"

RUN pip install -e . --no-deps 2>&1 | tail -5

ENV PYTHONPATH=/workspace/repo:$PYTHONPATH

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

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
