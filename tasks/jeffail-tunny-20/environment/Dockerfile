FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

RUN git config --global user.email "build@example.com" && git config --global user.name "Build"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/Jeffail/tunny.git && \
    git fetch --depth 30 origin 452a8e97d6a39fdd8fb5fdf1327eed2bf0c3429c && \
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

RUN printf '%s\n' '#!/bin/bash' 'REAL_GIT=/usr/bin/git' 'if [ "$1" = "apply" ] && echo "$*" | grep -q "golden.patch"; then' '  PF=$(for a in "$@"; do echo "$a"; done | grep "golden.patch" | head -1)' '  if [ -n "$PF" ]; then' '    python3 -c "import sys,re; c=open(sys.argv[1]).read(); s=re.split(chr(100)+chr(105)+chr(102)+chr(102)+chr(32)+chr(45)+chr(45)+chr(103)+chr(105)+chr(116)+chr(32),c); f=[x for x in s if not x.startswith(\"a/tunny_logo.png\")]; open(\"/tmp/gf.patch\",\"w\").write(\"diff --git \".join(f))" "$PF"' '    exec $REAL_GIT apply /tmp/gf.patch' '  fi' 'fi' 'exec $REAL_GIT "$@"' > /usr/local/bin/git && chmod +x /usr/local/bin/git

WORKDIR /workspace/repo

WORKDIR /workspace

# anti-leak: prune future commits/tags/branches/remotes, keep only base_commit ancestors
RUN cd /workspace/repo && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    (git remote | xargs -r -n1 git remote remove || true) && \
    cur="$(git rev-parse --abbrev-ref HEAD)" && \
    git for-each-ref --format='%(refname:short)' refs/heads | while read -r b; do [ "$b" = "$cur" ] || git branch -D "$b"; done && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive

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
