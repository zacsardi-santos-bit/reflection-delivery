FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates unzip patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://storage.googleapis.com/dart-archive/channels/stable/release/2.7.2/sdk/dartsdk-linux-x64-release.zip \
    -o /tmp/dart-sdk.zip && \
    unzip /tmp/dart-sdk.zip -d /opt && \
    rm /tmp/dart-sdk.zip

ENV PATH="/opt/dart-sdk/bin:${PATH}"
ENV PUB_CACHE="/root/.pub-cache"

RUN mkdir -p /workspace/test-results

RUN cat > /workspace/dart_to_junit.py << 'PYEOF'
#!/usr/bin/env python3
import json, sys, os
import xml.etree.ElementTree as ET

def main():
    input_file = sys.argv[1] if len(sys.argv) > 1 else '-'
    output_file = '/workspace/test-results/output.xml'
    if input_file == '-':
        content = sys.stdin.read()
    else:
        try:
            with open(input_file, 'r', errors='replace') as f:
                content = f.read()
        except IOError:
            content = ''
    tests = {}
    for line in content.split('\n'):
        line = line.strip()
        if not line:
            continue
        try:
            event = json.loads(line)
        except json.JSONDecodeError:
            continue
        etype = event.get('type', '')
        if etype == 'testStart':
            t = event.get('test', {})
            tid = t.get('id')
            if tid is not None:
                tests[tid] = {'name': t.get('name',''), 'result':'unknown', 'error':'', 'hidden': t.get('hidden', False)}
        elif etype == 'error':
            tid = event.get('testID')
            if tid in tests:
                tests[tid]['error'] = event.get('error', '')
        elif etype == 'testDone':
            tid = event.get('testID')
            if tid in tests:
                tests[tid]['result'] = event.get('result', 'failure')
                if event.get('hidden', False):
                    tests[tid]['hidden'] = True
    skip_suffixes = ['(setUpAll)', '(tearDownAll)', '(setUp)', '(tearDown)']
    visible = [(tid, info) for tid, info in tests.items()
               if not info.get('hidden', False)
               and not any(info['name'].endswith(s) for s in skip_suffixes)]
    failures = 0
    root = ET.Element('testsuite', name='dart_tests', tests=str(len(visible)))
    for tid, info in visible:
        tc = ET.SubElement(root, 'testcase', name=info['name'], classname='dart_test')
        if info['result'] in ('failure', 'error'):
            failures += 1
            fe = ET.SubElement(tc, 'failure')
            fe.set('message', info.get('error','Test failed')[:200])
            fe.text = info.get('error', '')
    root.set('failures', str(failures))
    os.makedirs(os.path.dirname(output_file), exist_ok=True)
    ET.ElementTree(root).write(output_file, encoding='unicode', xml_declaration=True)

if __name__ == '__main__':
    main()
PYEOF

RUN cat > /workspace/run_dart_tests.sh << 'SHEOF'
#!/bin/bash
mkdir -p /workspace/test-results
cd /workspace/repo/packages/flutter_tools
FLUTTER_ROOT=/workspace/repo pub run test -rjson \
    test/general.shard/crash_reporting_test.dart \
    test/general.shard/flutter_manifest_test.dart \
    test/general.shard/github_template_test.dart \
    test/general.shard/runner/runner_test.dart \
    > /tmp/dart_output.json 2>/tmp/dart_errors.txt
EXIT_CODE=$?
python3 /workspace/dart_to_junit.py /tmp/dart_output.json
exit $EXIT_CODE
SHEOF

RUN chmod +x /workspace/run_dart_tests.sh

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 0190e40457d43e17bdfaf046dfa634cbc5bf28b9 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cd /workspace/repo/packages/flutter_tools && FLUTTER_ROOT=/workspace/repo pub get 2>&1 | tail -5

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
