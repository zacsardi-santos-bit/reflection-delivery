FROM golang:1.21

RUN apt-get update && apt-get install -y git curl ca-certificates patch build-essential && rm -rf /var/lib/apt/lists/*

ENV GO111MODULE=off
ENV GOPATH=/go

RUN mkdir -p /go/src/github.com/qri-io

RUN git init /go/src/github.com/qri-io/dataset && cd /go/src/github.com/qri-io/dataset && \
    git remote add origin https://github.com/qri-io/dataset.git && \
    git fetch --depth 30 origin 9ed5d73e31243c0f7c1db026f5ea3ac59682ab13 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN git clone https://github.com/ipfs/go-datastore.git /go/src/github.com/ipfs/go-datastore && \
    cd /go/src/github.com/ipfs/go-datastore && git checkout v0.0.5

RUN git clone https://github.com/google/uuid.git /go/src/github.com/google/uuid

RUN git clone https://github.com/jbenet/goprocess.git /go/src/github.com/jbenet/goprocess && \
    cd /go/src/github.com/jbenet/goprocess && git checkout v0.1.0

RUN git clone https://github.com/jbenet/go-base58.git /go/src/github.com/jbenet/go-base58

RUN git clone https://github.com/multiformats/go-multihash.git /go/src/github.com/multiformats/go-multihash && \
    cd /go/src/github.com/multiformats/go-multihash && git checkout v0.0.9

RUN git clone https://github.com/minio/blake2b-simd.git /go/src/github.com/minio/blake2b-simd

RUN git clone https://github.com/minio/sha256-simd.git /go/src/github.com/minio/sha256-simd && \
    cd /go/src/github.com/minio/sha256-simd && git checkout v0.1.0

RUN git clone https://github.com/mr-tron/base58.git /go/src/github.com/mr-tron/base58

RUN git clone https://github.com/spaolacci/murmur3.git /go/src/github.com/spaolacci/murmur3

RUN git clone https://github.com/golang/crypto.git /go/src/golang.org/x/crypto && \
    cd /go/src/golang.org/x/crypto && git checkout 5c40567a22f8

RUN git clone https://github.com/golang/sys.git /go/src/golang.org/x/sys && \
    cd /go/src/golang.org/x/sys && git checkout 2204b6615fb84fc69a9e5d37b2d508ddf544dcea

RUN git clone https://github.com/qri-io/jsonschema.git /go/src/github.com/qri-io/jsonschema && \
    cd /go/src/github.com/qri-io/jsonschema && git checkout v0.1.1

RUN git clone https://github.com/qri-io/jsonpointer.git /go/src/github.com/qri-io/jsonpointer && \
    cd /go/src/github.com/qri-io/jsonpointer && git checkout v0.1.0

RUN mkdir -p /workspace && ln -s /go/src/github.com/qri-io/dataset /workspace/repo

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
