FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    python3 python3-pip python3-venv python3-dev \
    libpq-dev postgresql postgresql-client \
    libffi-dev libssl-dev \
    locales \
    && locale-gen en_US.UTF-8 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/pypi/warehouse.git && \
    git fetch --depth 30 origin 1775af7fb833f793e9e2114c808a2f2c99961a2e && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN python3 -m pip install --break-system-packages --upgrade setuptools wheel 2>&1 | tail -5 || true

RUN python3 -m pip install --break-system-packages \
    -r requirements/main.txt \
    -r requirements/tests.txt \
    2>&1 | tail -5 || true

RUN python3 -m pip install --break-system-packages -e . 2>&1 | tail -5 || true

RUN mkdir -p warehouse/admin/static/dist/ warehouse/static/dist/

RUN service postgresql start && \
    sleep 2 && \
    sed -i 's/md5/trust/g; s/peer/trust/g; s/scram-sha-256/trust/g' /etc/postgresql/*/main/pg_hba.conf && \
    service postgresql restart && \
    sleep 2 && \
    su postgres -c "psql -c \"CREATE ROLE root SUPERUSER LOGIN;\"" && \
    service postgresql stop

ENV PYTHONPATH=/workspace/repo

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
