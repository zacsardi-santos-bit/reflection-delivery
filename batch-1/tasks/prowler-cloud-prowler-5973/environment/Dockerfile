FROM python:3.12

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    postgresql postgresql-client libpq-dev \
    && rm -rf /var/lib/apt/lists/*

RUN pip install poetry

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/prowler-cloud/prowler.git && \
    git fetch --depth 30 origin e09a04d5931bfb6b85e7d97425c9186fc75778b5 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN sed -i 's|prowler = {git = "https://github.com/prowler-cloud/prowler.git", branch = "master"}|prowler = {path = "..", develop = true}|' /workspace/repo/api/pyproject.toml && \
    rm -f /workspace/repo/api/poetry.lock

WORKDIR /workspace/repo/api

RUN poetry config virtualenvs.create false && \
    poetry install --no-interaction 2>&1 | tail -30

RUN pip install setuptools

RUN find /etc/postgresql -name pg_hba.conf -exec sed -i 's/scram-sha-256/trust/g; s/md5/trust/g; s/peer/trust/g' {} \; && \
    service postgresql start && sleep 3 && \
    su -c "psql -c \"CREATE ROLE prowler WITH SUPERUSER LOGIN PASSWORD 'S3cret';\"" postgres && \
    service postgresql stop

ENV DJANGO_SETTINGS_MODULE=config.django.testing
ENV PYTHONPATH=/workspace/repo/api/src/backend
ENV DJANGO_TOKEN_SIGNING_KEY=test-hs256-signing-key-for-prowler-testing

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
