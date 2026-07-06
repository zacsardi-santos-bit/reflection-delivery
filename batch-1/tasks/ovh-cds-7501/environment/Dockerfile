FROM golang:1.24

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    postgresql redis-server \
    && rm -rf /var/lib/apt/lists/*

RUN sed -i 's/scram-sha-256/trust/g' /etc/postgresql/*/main/pg_hba.conf && \
    sed -i 's/md5/trust/g' /etc/postgresql/*/main/pg_hba.conf

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ovh/cds.git && \
    git fetch --depth 30 origin f91148e353ebbe498b6be99988ce1ab24de852d0 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download

RUN go install github.com/rubenv/sql-migrate/sql-migrate@v1.5.2

RUN mkdir -p /root/.cds && printf '{"dbDriver":"postgres","dbUser":"cds","dbPassword":"cds","dbName":"cds","dbSchema":"public","dbHost":"localhost","dbPort":"5432","sslMode":"disable","redisHost":"localhost:6379","redisPassword":"","redisDbIndex":"0"}' > /root/.cds/api.tests.cfg.json

RUN printf 'development:\n  dialect: postgres\n  datasource: "host=localhost port=5432 dbname=cds user=cds sslmode=disable"\n  dir: /workspace/repo/engine/sql/api\n  table: gorp_migrations\n' > /workspace/dbconfig.yml

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
