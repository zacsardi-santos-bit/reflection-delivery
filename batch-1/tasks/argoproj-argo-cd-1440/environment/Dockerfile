FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/argoproj/argo-cd.git && \
    git fetch --depth 30 origin 56cd8fcc95094dbedd91f8a0e737d8de7992ba30 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

ENV GOPATH=/go
ENV GO111MODULE=off

ENV DEP_VERSION=0.5.0
RUN curl -fsSL -o /usr/local/bin/dep https://github.com/golang/dep/releases/download/v${DEP_VERSION}/dep-linux-amd64 && \
    chmod +x /usr/local/bin/dep

RUN mkdir -p /go/src/dummy && \
    cp /workspace/repo/Gopkg.toml /go/src/dummy/Gopkg.toml && \
    cp /workspace/repo/Gopkg.lock /go/src/dummy/Gopkg.lock && \
    cd /go/src/dummy && \
    dep ensure -vendor-only && \
    cp -r vendor/. /go/src/ && \
    rm -rf vendor

RUN rm -rf /go/src/k8s.io/kube-openapi && \
    git clone https://github.com/kubernetes/kube-openapi /go/src/k8s.io/kube-openapi && \
    cd /go/src/k8s.io/kube-openapi && \
    COMMIT=$(git log --before="2019-10-01" --format="%H" -1) && \
    git checkout $COMMIT

RUN mkdir -p /go/src/github.com/argoproj && \
    ln -s /workspace/repo /go/src/github.com/argoproj/argo-cd

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
