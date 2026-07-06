FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential unzip xz-utils python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 955921479ba1377afcff9b7d04b7f2379aa81738 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

ENV PATH="/workspace/repo/bin:${PATH}"
ENV PUB_CACHE="/opt/pub-cache"

RUN mkdir -p /opt/pub-cache

RUN cd /workspace/repo/packages/flutter_tools && flutter pub get 2>&1 | tail -50

RUN python3 - << 'PYEOF'
script = """import sys, json
names = {}
for line in sys.stdin:
    line = line.strip()
    if not line:
        continue
    try:
        event = json.loads(line)
        t = event.get('type')
        if t == 'testStart':
            tid = event['test']['id']
            names[tid] = event['test']['name']
        elif t == 'testDone':
            tid = event['testID']
            result = event.get('result', '')
            name = names.get(tid, 'test_' + str(tid))
            if name.startswith('loading ') or event.get('hidden', False):
                continue
            if result == 'success':
                print(json.dumps({'Action': 'pass', 'Test': name, 'Elapsed': 0.0}))
            else:
                print(json.dumps({'Action': 'fail', 'Test': name, 'Elapsed': 0.0}))
    except Exception:
        pass
"""
with open('/workspace/dart_to_go.py', 'w') as f:
    f.write(script)
PYEOF

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
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# offline: ensure verifier-required toolchain (go) present on default/login PATH
RUN (command -v go >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends golang-go && rm -rf /var/lib/apt/lists/*) \
    || (apk add --no-cache go) || (dnf install -y golang) || (yum install -y golang) || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
