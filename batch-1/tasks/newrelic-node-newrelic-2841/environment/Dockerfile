FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 make g++ \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/newrelic/node-newrelic.git && \
    git fetch --depth 30 origin ed89f387efe2c5c3175250684bf4987ed29d7747 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN npm install --ignore-scripts 2>&1 | tail -5

RUN printf '%s\n' \
    '#!/usr/bin/env python3' \
    'import subprocess, sys, json, re, os' \
    '' \
    'test_files = [a for a in sys.argv[1:] if a.endswith(".js") or a.endswith(".ts")]' \
    'output_path = "/workspace/test-results/output.json"' \
    'for a in sys.argv[1:]:' \
    '    if a.startswith("output="):' \
    '        output_path = a[7:]' \
    '' \
    'result = subprocess.run(' \
    '    ["node", "--test"] + test_files,' \
    '    capture_output=True, text=True, cwd="/workspace/repo",' \
    '    env=os.environ.copy()' \
    ')' \
    '' \
    'out = result.stdout + result.stderr' \
    'passes, failures = [], []' \
    'for line in out.split("\n"):' \
    '    m = re.match(r"^(not )?ok \d+ - (.+?)(?:\s+#.*)?$", line)' \
    '    if not m: continue' \
    '    name = m.group(2).strip()' \
    '    if m.group(1): failures.append({"fullTitle": name, "file": ""})' \
    '    else: passes.append({"fullTitle": name, "file": ""})' \
    '' \
    'mocha_json = {"stats": {"passes": len(passes), "failures": len(failures), "pending": 0}, "passes": passes, "failures": failures, "pending": []}' \
    'os.makedirs(os.path.dirname(output_path) or ".", exist_ok=True)' \
    'with open(output_path, "w") as f: json.dump(mocha_json, f)' \
    'sys.exit(result.returncode)' \
    > /usr/local/bin/node-test-json && \
    chmod +x /usr/local/bin/node-test-json

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
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
