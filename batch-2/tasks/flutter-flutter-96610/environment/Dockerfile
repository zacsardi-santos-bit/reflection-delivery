FROM dart:2.17

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN cat > /workspace/dart_to_junit.py << 'PYEOF'
import sys, json

lines = sys.stdin.read().splitlines()
testcases = []
test_names = {}

for line in lines:
    try:
        obj = json.loads(line)
    except Exception:
        continue
    t = obj.get('type')
    if t == 'testStart':
        test = obj['test']
        name = test['name']
        if not test.get('hidden', False) and not name.startswith('loading '):
            test_names[test['id']] = name
    elif t == 'testDone':
        test_id = obj['testID']
        if test_id in test_names:
            name = test_names[test_id]
            result = obj.get('result', 'error')
            skipped = obj.get('skipped', False)
            if skipped:
                status = 'skipped'
            elif result == 'success':
                status = 'passed'
            else:
                status = 'failed'
            testcases.append((name, status))

failures = sum(1 for _, s in testcases if s == 'failed')
print('<?xml version="1.0" encoding="UTF-8"?>')
print('<testsuite name="dart" tests="{}" failures="{}">'.format(len(testcases), failures))
for name, status in testcases:
    safe = name.replace('&', '&amp;').replace('"', '&quot;').replace('<', '&lt;').replace('>', '&gt;')
    if status == 'failed':
        print('<testcase name="{}"><failure/></testcase>'.format(safe))
    elif status == 'skipped':
        print('<testcase name="{}"><skipped/></testcase>'.format(safe))
    else:
        print('<testcase name="{}"/>'.format(safe))
print('</testsuite>')
PYEOF

RUN cat > /workspace/run_dart_test.sh << 'SHEOF'
#!/bin/bash
dart test --reporter json "$@" > /tmp/dart_out.json 2>&1
DART_EXIT=$?
python3 /workspace/dart_to_junit.py < /tmp/dart_out.json
exit $DART_EXIT
SHEOF

RUN chmod +x /workspace/run_dart_test.sh

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 2df7dca858f3cd05da75f91f596441cc8a5a9b44 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/packages/flutter_tools

RUN dart pub get

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
