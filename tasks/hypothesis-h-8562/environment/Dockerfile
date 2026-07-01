FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch \
    build-essential gcc python3-dev libpq-dev libffi-dev \
    postgresql postgresql-client \
    && rm -rf /var/lib/apt/lists/*

RUN PGVER=$(ls /usr/lib/postgresql/ 2>/dev/null | head -1) && \
    pg_createcluster $PGVER main 2>/dev/null || true && \
    mkdir -p /etc/postgresql/$PGVER/main && \
    printf "local all all trust\nhost all all all trust\n" > /etc/postgresql/$PGVER/main/pg_hba.conf

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/hypothesis/h.git && \
    git fetch --depth 30 origin 05be632ac4687c2029cdb749ec0369f0f33e497e && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --no-cache-dir -r requirements/tests.txt

ENV DATABASE_URL=postgresql://postgres@localhost/h_tests
ENV AUTHORITY=example.com
ENV SQLALCHEMY_SILENCE_UBER_WARNING=1
ENV PYTHONPATH=/workspace/repo

RUN service postgresql start && \
    sleep 3 && \
    su postgres -c "createdb h_tests" && \
    python3 -m h.scripts.init_db --delete --create && \
    service postgresql stop

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
