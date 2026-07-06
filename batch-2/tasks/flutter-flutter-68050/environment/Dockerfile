FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential unzip python3 \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://storage.googleapis.com/dart-archive/channels/stable/release/2.10.5/sdk/dartsdk-linux-x64-release.zip \
    -o /tmp/dart.zip && unzip /tmp/dart.zip -d /usr/lib && rm /tmp/dart.zip

ENV PATH="/usr/lib/dart-sdk/bin:${PATH}"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 537cf33a21773e4ec6a4bd620a08e9fe9cfdfe99 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cd /workspace/repo/packages/flutter_tools && pub get

RUN cat > /usr/local/bin/dart_to_junit.py << 'EOF'
import json, sys
tests = {}
errors = {}
starts = {}
for line in sys.stdin:
    line = line.strip()
    if not line:
        continue
    try:
        ev = json.loads(line)
    except Exception:
        continue
    t = ev.get('type', '')
    if t == 'testStart':
        tid = ev['test']['id']
        tests[tid] = {'name': ev['test']['name'], 'result': None, 'hidden': False}
        starts[tid] = ev.get('time', 0)
    elif t == 'error':
        errors[ev.get('testID', 0)] = ev.get('error', '')
    elif t == 'testDone':
        tid = ev.get('testID', 0)
        if tid in tests:
            tests[tid]['result'] = ev.get('result', 'success')
            tests[tid]['hidden'] = ev.get('hidden', False)
            tests[tid]['time'] = (ev.get('time', 0) - starts.get(tid, 0)) / 1000.0

print('<?xml version="1.0"?>')
print('<testsuite name="dart">')
for tid, t in tests.items():
    r = t.get('result')
    if r is None or t.get('hidden', False):
        continue
    n = t['name'].replace('&', '&amp;').replace('<', '&lt;').replace('>', '&gt;').replace('"', '&quot;')
    dur = t.get('time', 0)
    if r == 'success':
        print('<testcase name="' + n + '" time="' + str(dur) + '"/>')
    else:
        err = errors.get(tid, 'test failed').replace('&', '&amp;').replace('<', '&lt;').replace('>', '&gt;')
        print('<testcase name="' + n + '" time="' + str(dur) + '"><failure>' + err + '</failure></testcase>')
print('</testsuite>')
EOF

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
