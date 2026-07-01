FROM golang:1.23

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/nuclio/nuclio.git && \
    git fetch --depth 30 origin b1501dd371d426c16911414f51c5c98fe1e50443 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

ENV GO111MODULE=off
ENV GOPATH=/go

RUN mkdir -p /go/src/github.com/nuclio && \
    ln -s /workspace/repo /go/src/github.com/nuclio/nuclio

RUN git clone https://github.com/nuclio/logger.git /go/src/github.com/nuclio/logger 2>&1 | tail -3 && \
    cd /go/src/github.com/nuclio/logger && \
    COMMIT=$(git log --before="2019-01-01" -1 --format="%H") && \
    [ -n "$COMMIT" ] && git checkout "$COMMIT" || true

RUN git clone https://github.com/nuclio/nuclio-sdk-go.git /go/src/github.com/nuclio/nuclio-sdk-go 2>&1 | tail -3 && \
    cd /go/src/github.com/nuclio/nuclio-sdk-go && \
    COMMIT=$(git log --before="2019-01-01" -1 --format="%H") && \
    [ -n "$COMMIT" ] && git checkout "$COMMIT" || true

RUN printf '#!/bin/sh\n[ "$1" = "version" ] && echo "Docker version 19.03.0"\nexit 0\n' > /usr/local/bin/docker && chmod +x /usr/local/bin/docker

WORKDIR /workspace/repo

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

# --- offline-harbor go-bake v1 ---
RUN set +e; cd /workspace/repo && for gm in $(find . -name go.mod -not -path '*/vendor/*' -not -path '*/testdata/*' -not -path '*/node_modules/*' 2>/dev/null); do d=$(dirname "$gm"); ( cd "$d" && GOWORK=off GOFLAGS=-mod=mod go mod download all 2>/dev/null ); done; true
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./pkg/processor/build/test 2>/dev/null; true
ENV GOWORK=off
ENV GOFLAGS=-mod=mod
ENV GOPROXY=off
ENV GOSUMDB=off

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
