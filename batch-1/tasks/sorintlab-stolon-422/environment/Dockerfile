FROM golang:1.21

RUN apt-get update && apt-get install -y git curl ca-certificates patch build-essential wget gnupg && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg && \
    echo "deb http://apt.postgresql.org/pub/repos/apt/ bookworm-pgdg main" > /etc/apt/sources.list.d/pgdg.list && \
    apt-get update && apt-get install -y postgresql-9.6 && rm -rf /var/lib/apt/lists/*

RUN wget -q https://github.com/etcd-io/etcd/releases/download/v3.3.27/etcd-v3.3.27-linux-amd64.tar.gz -O /tmp/etcd.tar.gz && \
    tar -xzf /tmp/etcd.tar.gz -C /tmp && \
    cp /tmp/etcd-v3.3.27-linux-amd64/etcd /usr/local/bin/etcd && \
    rm -rf /tmp/etcd*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/sorintlab/stolon.git && \
    git fetch --depth 30 origin 81790618e5fe18ef8aa499cdf5c51981d974d5de && \
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
ENV GOPATH=/workspace/repo/gopath

RUN mkdir -p /workspace/repo/gopath/src/github.com/sorintlab && \
    ln -s /workspace/repo /workspace/repo/gopath/src/github.com/sorintlab/stolon

RUN mkdir -p /workspace/repo/bin && \
    CGO_ENABLED=0 go build -o /workspace/repo/bin/stolon-sentinel github.com/sorintlab/stolon/cmd/sentinel && \
    CGO_ENABLED=0 go build -o /workspace/repo/bin/stolon-proxy github.com/sorintlab/stolon/cmd/proxy && \
    CGO_ENABLED=0 go build -o /workspace/repo/bin/stolonctl github.com/sorintlab/stolon/cmd/stolonctl && \
    go build -o /workspace/repo/bin/stolon-keeper github.com/sorintlab/stolon/cmd/keeper

ENV STKEEPER_BIN=/workspace/repo/bin/stolon-keeper
ENV STSENTINEL_BIN=/workspace/repo/bin/stolon-sentinel
ENV STPROXY_BIN=/workspace/repo/bin/stolon-proxy
ENV STCTL_BIN=/workspace/repo/bin/stolonctl
ENV ETCD_BIN=/usr/local/bin/etcd
ENV STOLON_TEST_STORE_BACKEND=etcdv2
ENV PATH=/usr/lib/postgresql/9.6/bin:$PATH

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
