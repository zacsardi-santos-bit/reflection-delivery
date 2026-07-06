FROM dart:latest

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential unzip python3 \
    && rm -rf /var/lib/apt/lists/*

RUN cat > /usr/local/bin/dart_to_junit.py << 'PYEOF'
import sys, json, argparse
import xml.etree.ElementTree as ET

ap = argparse.ArgumentParser()
ap.add_argument('--junitxml', default=None)
args, _ = ap.parse_known_args()

lines = sys.stdin.readlines()
tests = {}
order = []
for line in lines:
    line = line.strip()
    if not line:
        continue
    try:
        event = json.loads(line)
        t = event.get('type', '')
        if t == 'testStart':
            tid = event['test']['id']
            name = event['test']['name']
            if not name.startswith('loading'):
                tests[tid] = {'name': name, 'result': None, 'error': ''}
                order.append(tid)
        elif t == 'error':
            tid = event.get('testID')
            if tid and tid in tests:
                tests[tid]['error'] += event.get('error', '')
        elif t == 'testDone':
            tid = event['testID']
            if tid in tests:
                tests[tid]['result'] = event.get('result', 'error')
    except Exception:
        pass

ordered = [t for t in order if t in tests]
failures = sum(1 for tid in ordered if tests[tid]['result'] != 'success')

root = ET.Element('testsuites')
suite = ET.SubElement(root, 'testsuite',
    name='dart_test',
    tests=str(len(ordered)),
    failures=str(failures),
    errors='0',
    skipped='0')

for tid in ordered:
    t = tests[tid]
    tc = ET.SubElement(suite, 'testcase', name=t['name'], classname='dart_test')
    if t['result'] != 'success':
        msg = (t['error'] or 'Test failed')[:200]
        fail = ET.SubElement(tc, 'failure', message=msg)
        fail.text = t['error'] or 'Test failed'

xml_str = '<?xml version="1.0" encoding="UTF-8"?>\n' + ET.tostring(root, encoding='unicode')

if args.junitxml:
    with open(args.junitxml, 'w') as f:
        f.write(xml_str)
else:
    print(xml_str)
PYEOF

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 408f4f20313073b00ef2e64bc73093eb2d4e4c75 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cd packages/flutter_tools && dart pub get

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
