FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential unzip python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 5722adf11dfc2f272146001ebe9e0b4c4e8ce9d3 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN bash /workspace/repo/bin/internal/update_dart_sdk.sh

ENV PATH="/workspace/repo/bin/cache/dart-sdk/bin:$PATH"
ENV PUB_CACHE="/root/.pub-cache"

RUN cd /workspace/repo/packages/flutter_tools && pub get

RUN cat > /workspace/dart_to_bun.py << 'PYEOF'
import sys
import json

tests = {}

for line in sys.stdin:
    line = line.strip()
    if not line:
        continue
    try:
        event = json.loads(line)
        etype = event.get('type', '')
        if etype == 'testStart':
            test = event.get('test', {})
            tid = test.get('id')
            name = test.get('name', 'test_' + str(tid))
            tests[tid] = name
        elif etype == 'testDone':
            tid = event.get('testID')
            if event.get('hidden', False):
                continue
            name = tests.get(tid, 'test_' + str(tid))
            result = event.get('result', 'error')
            skipped = event.get('skipped', False)
            if not skipped:
                if result == 'success':
                    print('✓ ' + name)
                else:
                    print('✗ ' + name)
    except Exception:
        pass
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

# offline: ensure verifier-required toolchain (node) present on default/login PATH
RUN (command -v node >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends nodejs && rm -rf /var/lib/apt/lists/*) \
    || (apk add --no-cache nodejs) || (dnf install -y nodejs) || (yum install -y nodejs) || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
