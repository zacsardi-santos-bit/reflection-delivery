FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN echo 'import sys' > /usr/local/bin/fp.py \
    && echo 'lines = open(sys.argv[1]).readlines()' >> /usr/local/bin/fp.py \
    && echo 'sections = []' >> /usr/local/bin/fp.py \
    && echo 'current = []' >> /usr/local/bin/fp.py \
    && echo 'for line in lines:' >> /usr/local/bin/fp.py \
    && echo '    if line.startswith("diff --git "):' >> /usr/local/bin/fp.py \
    && echo '        if current:' >> /usr/local/bin/fp.py \
    && echo '            sections.append(current)' >> /usr/local/bin/fp.py \
    && echo '        current = [line]' >> /usr/local/bin/fp.py \
    && echo '    else:' >> /usr/local/bin/fp.py \
    && echo '        current.append(line)' >> /usr/local/bin/fp.py \
    && echo 'if current:' >> /usr/local/bin/fp.py \
    && echo '    sections.append(current)' >> /usr/local/bin/fp.py \
    && echo 'out = []' >> /usr/local/bin/fp.py \
    && echo 'for section in sections:' >> /usr/local/bin/fp.py \
    && echo '    if any("Binary files" in l and "differ" in l for l in section) and not any("GIT binary patch" in l for l in section):' >> /usr/local/bin/fp.py \
    && echo '        continue' >> /usr/local/bin/fp.py \
    && echo '    out.extend(section)' >> /usr/local/bin/fp.py \
    && echo 'sys.stdout.write("".join(out))' >> /usr/local/bin/fp.py \
    && chmod +x /usr/local/bin/fp.py

RUN mv /usr/bin/git /usr/bin/git.real \
    && echo '#!/bin/bash' > /usr/local/bin/git \
    && echo 'if [ "$1" = "apply" ] && [ -n "$2" ] && [ -f "$2" ]; then' >> /usr/local/bin/git \
    && echo '    FILTERED=$(mktemp /tmp/fp.XXXXXX)' >> /usr/local/bin/git \
    && echo '    python3 /usr/local/bin/fp.py "$2" > "$FILTERED"' >> /usr/local/bin/git \
    && echo '    /usr/bin/git.real apply "$FILTERED"' >> /usr/local/bin/git \
    && echo '    S=$?; rm -f "$FILTERED"; exit $S' >> /usr/local/bin/git \
    && echo 'else' >> /usr/local/bin/git \
    && echo '    exec /usr/bin/git.real "$@"' >> /usr/local/bin/git \
    && echo 'fi' >> /usr/local/bin/git \
    && chmod +x /usr/local/bin/git

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/grafana/beyla.git && \
    git fetch --depth 30 origin d3dc33ffa0c50b05e369572607c74bb9169f2643 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download || true

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
