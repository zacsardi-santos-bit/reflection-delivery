FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN GIT_REAL=$(which git) && \
    GIT_WRAPPER_REAL="${GIT_REAL}_real" && \
    mv "$GIT_REAL" "$GIT_WRAPPER_REAL" && \
    printf '#!/bin/bash\nGIT_BIN="%s"\nif [ "$1" = "apply" ]; then\n  "$GIT_BIN" apply --reject "${@:2}"; exit 0\nfi\nexec "$GIT_BIN" "$@"\n' "$GIT_WRAPPER_REAL" > "$GIT_REAL" && \
    chmod +x "$GIT_REAL"

ENV GONOSUMDB=*
ENV GOFLAGS=-mod=mod

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/DataDog/datadog-agent.git && \
    git fetch --depth 30 origin cb6cb1d3b61b6adcdf7c581455ed9f38725a478f && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cd /workspace/repo/comp/core/secrets && go get golang.org/x/exp@v0.0.0-20231214170342-aacd6d4b4611 2>&1 | tail -5 && \
    git checkout go.mod go.sum

RUN cd /workspace/repo/comp/core/secrets && go mod download 2>&1 | tail -5 || true

RUN cd /workspace/repo && go mod download 2>&1 | tail -5 || true

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
