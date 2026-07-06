FROM rust:1.94

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN curl -LsSf https://get.nexte.st/latest/linux | tar zxf - -C /usr/local/cargo/bin

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/astral-sh/ruff.git && \
    git fetch --depth 30 origin 62e1a60eb7cf5d90f2be011033531f3a917dcffc && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cargo fetch

RUN echo '#!/bin/bash' > /usr/local/bin/git && \
    echo 'REAL_GIT=/usr/bin/git' >> /usr/local/bin/git && \
    echo 'case "$1" in' >> /usr/local/bin/git && \
    echo '  apply)' >> /usr/local/bin/git && \
    echo '    exec "$REAL_GIT" apply --exclude=ruff.schema.json "${@:2}"' >> /usr/local/bin/git && \
    echo '    ;;' >> /usr/local/bin/git && \
    echo '  *)' >> /usr/local/bin/git && \
    echo '    exec "$REAL_GIT" "$@"' >> /usr/local/bin/git && \
    echo '    ;;' >> /usr/local/bin/git && \
    echo 'esac' >> /usr/local/bin/git && \
    chmod +x /usr/local/bin/git


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true
# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` (the client
# verifier exec) keeps go/cargo/java/etc on PATH after /etc/profile resets it.
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
