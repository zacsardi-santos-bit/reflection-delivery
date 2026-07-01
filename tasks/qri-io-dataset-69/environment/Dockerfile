FROM golang:1.21

RUN apt-get update && apt-get install -y git curl ca-certificates patch build-essential && rm -rf /var/lib/apt/lists/*

ENV GOPATH=/go
ENV GO111MODULE=off
ENV PATH=/go/bin:/usr/local/go/bin:$PATH

RUN mkdir -p /go/src/github.com/qri-io /go/src/github.com/ipfs /workspace

RUN git init /go/src/github.com/qri-io/dataset && cd /go/src/github.com/qri-io/dataset && \
    git remote add origin https://github.com/qri-io/dataset.git && \
    git fetch --depth 30 origin 8dcae3651ed256bf4ffe2a2db81370a4ef2ecf78 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN ln -s /go/src/github.com/qri-io/dataset /workspace/repo

RUN git clone https://github.com/ipfs/go-datastore.git /go/src/github.com/ipfs/go-datastore && \
    cd /go/src/github.com/ipfs/go-datastore && \
    git checkout 960127129e2130ba4821405dc32e2b3877c8834a

RUN mkdir -p /go/src/github.com/jbenet && \
    git clone https://github.com/jbenet/goprocess.git /go/src/github.com/jbenet/goprocess 2>&1 | tail -5 || true

RUN mkdir -p /go/src/github.com/satori && \
    git clone https://github.com/satori/go.uuid.git /go/src/github.com/satori/go.uuid 2>&1 | tail -5 && \
    cd /go/src/github.com/satori/go.uuid && \
    git checkout 0ef6afb2f6cdd6cdaeee3885a95099c63f18fc8c 2>&1 | tail -3 || true

RUN mkdir -p /go/src/golang.org/x && \
    git clone https://go.googlesource.com/crypto /go/src/golang.org/x/crypto 2>&1 | tail -5 && \
    cd /go/src/golang.org/x/crypto && \
    git checkout 750a45fe5e473d5afa193e9088f3d135e64eca26 2>&1 | tail -3

RUN mkdir -p /go/src/golang.org/x/sys && \
    git clone https://go.googlesource.com/sys /go/src/golang.org/x/sys 2>&1 | tail -5 && \
    cd /go/src/golang.org/x/sys && \
    git checkout 74cfc93a99be6ca6f193856132e6799065b071af 2>&1 | tail -3

RUN mkdir -p /go/src/github.com/multiformats && \
    git clone https://github.com/multiformats/go-multihash.git /go/src/github.com/multiformats/go-multihash 2>&1 | tail -5 && \
    cd /go/src/github.com/multiformats/go-multihash && \
    git checkout cb466b64a3eaf1d20cf7da6a34d97902de29d560 2>&1 | tail -3

RUN mkdir -p /go/src/github.com/jbenet && \
    git clone https://github.com/jbenet/go-base58.git /go/src/github.com/jbenet/go-base58 2>&1 | tail -5 || true

RUN mkdir -p /go/src/github.com/qri-io && \
    git clone https://github.com/qri-io/jsonschema.git /go/src/github.com/qri-io/jsonschema 2>&1 | tail -3 && \
    cd /go/src/github.com/qri-io/jsonschema && \
    git checkout 3e032f4c9ae99717fc14183cdb39b4cc8903c066 2>&1 | tail -2

RUN go get -v \
    github.com/qri-io/compare \
    github.com/qri-io/varName \
    github.com/qri-io/jsonpointer \
    github.com/datatogether/cdxj \
    github.com/sergi/go-diff/diffmatchpatch 2>&1 | tail -20 || true

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
