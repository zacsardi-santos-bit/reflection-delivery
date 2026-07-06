FROM golang:1.19

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    gnupg lsb-release \
    && rm -rf /var/lib/apt/lists/*

# Install PostgreSQL 11 (supports wal_keep_segments and recovery.conf used by this era of stolon)
RUN curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor -o /usr/share/keyrings/postgresql-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/postgresql-keyring.gpg] https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list \
    && apt-get update && apt-get install -y postgresql-11 postgresql-client-11 \
    && rm -rf /var/lib/apt/lists/*

RUN ln -s /usr/lib/postgresql/11/bin/initdb /usr/local/bin/initdb && \
    ln -s /usr/lib/postgresql/11/bin/pg_ctl /usr/local/bin/pg_ctl && \
    ln -s /usr/lib/postgresql/11/bin/postgres /usr/local/bin/postgres && \
    ln -s /usr/lib/postgresql/11/bin/pg_basebackup /usr/local/bin/pg_basebackup

RUN curl -L https://github.com/etcd-io/etcd/releases/download/v3.5.9/etcd-v3.5.9-linux-amd64.tar.gz -o /tmp/etcd.tar.gz \
    && tar xzf /tmp/etcd.tar.gz -C /tmp \
    && mv /tmp/etcd-v3.5.9-linux-amd64/etcd /usr/local/bin/ \
    && rm -rf /tmp/etcd*

RUN curl -fsSL https://github.com/tianon/gosu/releases/download/1.16/gosu-amd64 -o /usr/local/bin/gosu \
    && chmod +x /usr/local/bin/gosu

RUN useradd -m -s /bin/bash stolontest

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/sorintlab/stolon.git && \
    git fetch --depth 30 origin d95d53c44652cadf49298b5030a5e0a80e01dd48 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

ENV GO111MODULE=off
RUN GOPATH=/workspace/repo/gopath ./build

RUN chmod -R a+rX /workspace/repo && chmod a+rwx /workspace/repo/gopath/src/github.com/sorintlab

ENV STOLON_TEST_STORE_BACKEND=etcdv3
ENV ETCD_BIN=/usr/local/bin/etcd
ENV STKEEPER_BIN=/workspace/repo/bin/stolon-keeper
ENV STSENTINEL_BIN=/workspace/repo/bin/stolon-sentinel
ENV STPROXY_BIN=/workspace/repo/bin/stolon-proxy
ENV STCTL_BIN=/workspace/repo/bin/stolonctl

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
