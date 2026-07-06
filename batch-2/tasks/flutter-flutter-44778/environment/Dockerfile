FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates unzip xz-utils libstdc++6 python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 7caef218b55acac803aa67e7393e97f75ec70c5c && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

ENV PATH="/workspace/repo/bin:/workspace/repo/bin/cache/dart-sdk/bin:${PATH}"
ENV FLUTTER_ROOT=/workspace/repo
ENV PUB_CACHE=/workspace/repo/.pub-cache
ENV SWARMING_TASK_ID=1

RUN flutter config --no-analytics 2>&1 | tail -5

RUN flutter precache 2>&1 | tail -10

RUN cd /workspace/repo/packages/flutter && flutter pub get 2>&1 | tail -20

RUN cat > /workspace/flutter_to_junit.py << 'PYEOF'
import json,sys,xml.etree.ElementTree as ET
tests = {}
for line in sys.stdin:
    line = line.strip()
    if not line: continue
    try: ev = json.loads(line)
    except: continue
    t = ev.get('type')
    if t == 'testStart':
        test = ev.get('test', {}); tid = test.get('id')
        tests[tid] = {'name': test.get('name', str(tid)), 'status': 'passed', 'err': '', 'hidden': False}
    elif t == 'error':
        tid = ev.get('testID')
        if tid in tests:
            tests[tid]['err'] += (ev.get('error') or '') + '\n'
            tests[tid]['status'] = 'failed'
    elif t == 'testDone':
        tid = ev.get('testID')
        if tid in tests:
            if ev.get('hidden'): tests[tid]['hidden'] = True
            if ev.get('result', 'success') != 'success': tests[tid]['status'] = 'failed'
            if ev.get('skipped'): tests[tid]['status'] = 'skipped'
ts = ET.Element('testsuite')
for tid, td in tests.items():
    if td['hidden']: continue
    if not td['name']: continue
    tc = ET.SubElement(ts, 'testcase'); tc.set('classname', 'flutter'); tc.set('name', td['name'])
    if td['status'] == 'failed': f = ET.SubElement(tc, 'failure'); f.text = td['err'] or 'failed'
    elif td['status'] == 'skipped': ET.SubElement(tc, 'skipped')
print('<?xml version="1.0" encoding="utf-8"?>')
print(ET.tostring(ts, encoding='unicode'))
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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
