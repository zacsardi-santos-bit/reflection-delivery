FROM node:18

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential gnupg wget \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y redis-server && rm -rf /var/lib/apt/lists/*

RUN wget -q https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-debian12-7.0.14.tgz \
    && tar -xzf mongodb-linux-x86_64-debian12-7.0.14.tgz \
    && mv mongodb-linux-x86_64-debian12-7.0.14/bin/mongod /usr/local/bin/mongod \
    && rm -rf mongodb-linux-x86_64-debian12-7.0.14*

RUN wget -q https://github.com/etcd-io/etcd/releases/download/v3.5.14/etcd-v3.5.14-linux-amd64.tar.gz \
    && tar -xzf etcd-v3.5.14-linux-amd64.tar.gz \
    && mv etcd-v3.5.14-linux-amd64/etcd /usr/local/bin/etcd \
    && rm -rf etcd-v3.5.14-linux-amd64*

RUN wget -q https://downloads.mongodb.com/compass/mongosh-2.3.1-linux-x64.tgz \
    && tar -xzf mongosh-2.3.1-linux-x64.tgz \
    && cp mongosh-2.3.1-linux-x64/bin/* /usr/local/bin/ \
    && rm -rf mongosh-2.3.1-linux-x64*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/kube-HPC/hkube.git && \
    git fetch --depth 30 origin e4f47443860601cca4bc03df092509d717c9f410 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cd core/task-executor && npm ci
RUN cd core/pipeline-driver && npm ci

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
