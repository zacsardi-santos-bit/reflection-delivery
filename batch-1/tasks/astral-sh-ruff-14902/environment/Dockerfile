FROM rust:1.83

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/astral-sh/ruff.git && \
    git fetch --depth 30 origin 45b565cbb5b21e16ef58d1c649f7bb1f96cbad24 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cargo fetch

RUN cargo test -p ruff_linter --no-run

RUN cat > /workspace/parse_tests.py << 'PYEOF'
import sys
import re
import json

last = None
for line in sys.stdin:
    line = line.rstrip()
    m = re.match(r'^test (.+) \.\.\. ok', line)
    if m:
        name = m.group(1)
        sys.stdout.write(json.dumps({"Action": "run", "Test": name}) + "\n")
        sys.stdout.write(json.dumps({"Action": "pass", "Test": name, "Elapsed": 0.001}) + "\n")
        sys.stdout.flush()
        last = None
        continue
    m = re.match(r'^test (.+) \.\.\. FAILED', line)
    if m:
        name = m.group(1)
        sys.stdout.write(json.dumps({"Action": "run", "Test": name}) + "\n")
        sys.stdout.write(json.dumps({"Action": "fail", "Test": name, "Elapsed": 0.001}) + "\n")
        sys.stdout.flush()
        last = None
        continue
    m = re.match(r'^test (.+) \.\.\.', line)
    if m:
        last = m.group(1)
        continue
    if line.strip() == 'FAILED' and last:
        name = last
        sys.stdout.write(json.dumps({"Action": "run", "Test": name}) + "\n")
        sys.stdout.write(json.dumps({"Action": "fail", "Test": name, "Elapsed": 0.001}) + "\n")
        sys.stdout.flush()
        last = None
        continue
    last = None
PYEOF

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
