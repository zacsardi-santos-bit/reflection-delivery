FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential gcc g++ unzip \
    ocl-icd-libopencl1 ocl-icd-opencl-dev opencl-headers \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/spacemeshos/go-spacemesh.git && \
    git fetch --depth 30 origin c27a66679883e91f7cbf7b15fa28a34666b195fc && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN mkdir -p /workspace/repo/build && \
    curl -sSL https://github.com/spacemeshos/post-rs/releases/download/v0.6.6/libpost-linux-v0.6.6.zip \
    -o /tmp/libpost.zip && \
    unzip -o -j /tmp/libpost.zip -d /workspace/repo/build && \
    rm /tmp/libpost.zip && \
    curl -sSL https://github.com/spacemeshos/post-rs/releases/download/v0.6.6/service-linux-v0.6.6.zip \
    -o /tmp/service.zip && \
    unzip -o -j /tmp/service.zip -d /workspace/repo/build && \
    chmod +x /workspace/repo/build/service && \
    rm /tmp/service.zip

ENV CGO_ENABLED=1
ENV CGO_CFLAGS="-I/workspace/repo/build/"
ENV CGO_LDFLAGS="-L/workspace/repo/build/ -Wl,-rpath,/workspace/repo/build/"
ENV GOCACHE=/root/.cache/go-build

WORKDIR /workspace/repo

RUN go mod download || true

RUN go build ./p2p/server/... || true
RUN go build ./fetch/... || true
RUN go build ./activation/... || true
RUN go build ./syncer/... || true

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
