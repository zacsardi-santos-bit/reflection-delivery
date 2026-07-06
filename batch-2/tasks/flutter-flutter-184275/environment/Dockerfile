FROM dart:stable

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

RUN cat > /usr/local/bin/dart_to_junit << 'PYEOF'
#!/usr/bin/env python3
import sys
import json
import xml.etree.ElementTree as ET

def main():
    tests = {}
    results = []
    had_failure = False

    for line in sys.stdin:
        line = line.strip()
        if not line:
            continue
        try:
            event = json.loads(line)
        except (json.JSONDecodeError, ValueError):
            continue

        event_type = event.get('type', '')

        if event_type == 'testStart':
            test = event.get('test', {})
            test_id = test.get('id')
            test_name = test.get('name', '')
            if test_id is not None:
                tests[test_id] = test_name

        elif event_type == 'testDone':
            test_id = event.get('testID')
            hidden = event.get('hidden', False)
            if hidden or test_id not in tests:
                continue
            result = event.get('result', 'success')
            skipped = event.get('skipped', False)
            test_name = tests[test_id]
            results.append({'name': test_name, 'result': result, 'skipped': skipped})
            if not skipped and result != 'success':
                had_failure = True

    failures = sum(1 for r in results if not r['skipped'] and r['result'] != 'success')
    skipped = sum(1 for r in results if r['skipped'])
    suite = ET.Element('testsuite', name='dart_tests',
                       tests=str(len(results)),
                       failures=str(failures),
                       errors='0',
                       skipped=str(skipped))
    for r in results:
        tc = ET.SubElement(suite, 'testcase', classname='flutter_tools', name=r['name'])
        if r['skipped']:
            ET.SubElement(tc, 'skipped')
        elif r['result'] != 'success':
            ET.SubElement(tc, 'failure', message='Test failed')

    print('<?xml version="1.0" encoding="UTF-8"?>')
    print(ET.tostring(suite, encoding='unicode'))
    sys.exit(1 if had_failure else 0)

if __name__ == '__main__':
    main()
PYEOF
RUN chmod +x /usr/local/bin/dart_to_junit

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 79557800f99214fa78d7a77d1e273d39c9077933 && \
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

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
