FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential unzip python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin c051b745be95f78af66912954c3f0238b5a13f0a && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

ENV PATH="/workspace/repo/bin:$PATH"
ENV FLUTTER_ROOT="/workspace/repo"

RUN dart --version 2>&1

RUN cd /workspace/repo/packages/flutter_tools && dart pub get 2>&1 | tail -20

# cargoshard-flt-offline: flutter_tools is a pub-workspace member; `dart test` re-resolves the
# workspace + refreshes pub.dev version/advisory caches unless fully warmed at build. Resolve the
# workspace via `flutter pub get` at the repo root, then run the exact test once (network on) so
# the resolution/version caches + kernel/test snapshot are baked; offline `dart test` reuses them.
RUN cd /workspace/repo && (FLUTTER_ROOT=/workspace/repo ./bin/flutter pub get 2>&1 | tail -5 || true)
RUN cd /workspace/repo/packages/flutter_tools && \
    (FLUTTER_ROOT=/workspace/repo dart test test/general.shard/macos/darwin_dependency_management_test.dart 2>&1 | tail -5 || true)

RUN cat > /workspace/dart2nextest.py << 'PYEOF'
import sys
import json

tests = {}
for line in sys.stdin:
    line = line.strip()
    if not line:
        continue
    try:
        event = json.loads(line)
        t = event.get('type', '')
        if t == 'testStart':
            tests[event['test']['id']] = event['test']['name']
        elif t == 'testDone':
            tid = event.get('testID')
            name = tests.get(tid, '')
            if name and not name.startswith('loading '):
                r = event.get('result', 'success')
                status = 'PASS' if r == 'success' else 'FAIL'
                sys.stdout.write('{} [   0.000s] suite {}\n'.format(status, name))
                sys.stdout.flush()
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

# OFFLINE-CARGO-BAKE
RUN cd /workspace/repo && (cargo fetch 2>/dev/null; cargo test --no-run 2>/dev/null; cargo nextest run --no-run 2>/dev/null; true)
ENV CARGO_NET_OFFLINE=true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
