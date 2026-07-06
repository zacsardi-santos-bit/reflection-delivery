FROM python:3.11-bookworm

RUN apt-get update && apt-get install -y curl gnupg ca-certificates && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg \
    && echo "deb http://apt.postgresql.org/pub/repos/apt bookworm-pgdg main" > /etc/apt/sources.list.d/pgdg.list

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libgdal-dev libpq-dev \
    postgresql-15 postgresql-15-postgis-3 \
    redis-server \
    libcairo2-dev libpango1.0-dev libffi-dev shared-mime-info \
    && rm -rf /var/lib/apt/lists/*

RUN pip install poetry

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/pass-culture/pass-culture-main.git && \
    git fetch --depth 30 origin 7a1108b0b6de3927cf492d981b557dd495f20f7c && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/api

RUN poetry config virtualenvs.create false \
    && poetry install --with dev --no-root 2>&1 | tail -30

RUN pip install -e . --no-deps

RUN service postgresql start \
    && sleep 3 \
    && su -c "psql -c \"CREATE ROLE pytest WITH SUPERUSER LOGIN PASSWORD 'pytest';\"" postgres \
    && su -c "createdb -O pytest pass_culture" postgres \
    && service postgresql stop

ENV DATABASE_URL_TEST=postgresql://pytest:pytest@localhost:5432/pass_culture
ENV REDIS_URL=redis://localhost:6379
ENV RUN_ENV=tests

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
