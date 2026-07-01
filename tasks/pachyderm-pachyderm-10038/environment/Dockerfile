FROM golang:1.22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    postgresql \
    pgbouncer \
    && rm -rf /var/lib/apt/lists/*

RUN PG_CONF=$(ls /etc/postgresql/*/main/postgresql.conf | head -1) && \
    PG_HBA=$(ls /etc/postgresql/*/main/pg_hba.conf | head -1) && \
    echo "port = 30228" >> "$PG_CONF" && \
    echo "listen_addresses = '127.0.0.1'" >> "$PG_CONF" && \
    echo "max_connections = 500" >> "$PG_CONF" && \
    printf 'local all all trust\nhost all all 127.0.0.1/32 trust\nhost all all ::1/128 trust\n' > "$PG_HBA"

RUN service postgresql start && \
    sleep 3 && \
    su postgres -c "psql -p 30228 -c \"CREATE USER pachyderm WITH SUPERUSER;\"" && \
    su postgres -c "psql -p 30228 -c \"CREATE DATABASE pachyderm OWNER pachyderm;\"" && \
    service postgresql stop

RUN printf '[databases]\n* = host=127.0.0.1 port=30228\n\n[pgbouncer]\nlisten_port = 30229\nlisten_addr = 127.0.0.1\nunix_socket_dir =\nauth_type = trust\nauth_file = /etc/pgbouncer/userlist.txt\npool_mode = session\nmax_client_conn = 100000\ndefault_pool_size = 20\nlogfile = /tmp/pgbouncer.log\npidfile = /tmp/pgbouncer.pid\n' > /etc/pgbouncer/pgbouncer.ini && \
    printf '"pachyderm" "correcthorsebatterystable"\n' > /etc/pgbouncer/userlist.txt && \
    chmod 644 /etc/pgbouncer/pgbouncer.ini /etc/pgbouncer/userlist.txt

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/pachyderm/pachyderm.git && \
    git fetch --depth 30 origin 7f2fda697ef5a79234ffc116d07f3f918bd27da1 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

ENV SKIP_DOCKER_POSTGRES_CREATE=1
ENV DOCKER_MACHINE_NAME=127.0.0.1

RUN go mod download 2>&1 | tail -5

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
