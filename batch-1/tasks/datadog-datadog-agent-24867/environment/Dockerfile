FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN mv /usr/bin/git /usr/bin/git.real && \
    printf '#!/bin/bash\nif [ "$1" = "apply" ]; then\n  /usr/bin/git.real apply --reject "${@:2}" 2>&1\n  exit 0\nelse\n  exec /usr/bin/git.real "$@"\nfi\n' > /usr/bin/git && \
    chmod +x /usr/bin/git

WORKDIR /workspace

RUN git.real init /workspace/repo && cd /workspace/repo && \
    git.real remote add origin https://github.com/DataDog/datadog-agent.git && \
    git.real fetch --depth 30 origin eb78973e102240bbf8f9abb7a57f8e39de5f7c81 && \
    git.real checkout --detach FETCH_HEAD && \
    git.real submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git.real remote remove origin && \
    git.real tag -l | xargs -r git.real tag -d && \
    git.real for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git.real update-ref -d && \
    git.real reflog expire --expire=now --all && \
    git.real gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --no-cache-dir \
    invoke==2.2.0 \
    "python-gitlab>=3.0.0,<4.0.0" \
    "PyYAML==6.0.1" \
    "codeowners==0.6.0" \
    "requests==2.31.0" \
    pytest

ENV PYTHONPATH=/workspace/repo

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

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
