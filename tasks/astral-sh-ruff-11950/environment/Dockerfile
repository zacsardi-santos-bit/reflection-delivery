FROM rust:1.79

RUN apt-get update && apt-get install -y git python3 && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/astral-sh/ruff.git && \
    git fetch --depth 30 origin 88a4cc41f76cbb5d1318d5e689b118ec624b8d69 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN echo '#!/usr/bin/env python3' > /usr/local/bin/cft.py && \
    echo 'import subprocess, sys, os' >> /usr/local/bin/cft.py && \
    echo 'from xml.etree import ElementTree as ET' >> /usr/local/bin/cft.py && \
    echo 'args = sys.argv[1:]' >> /usr/local/bin/cft.py && \
    echo 'junitxml = None' >> /usr/local/bin/cft.py && \
    echo 'cargo = []' >> /usr/local/bin/cft.py && \
    echo 'skip = False' >> /usr/local/bin/cft.py && \
    echo 'for a in args:' >> /usr/local/bin/cft.py && \
    echo '    if skip: junitxml = a; skip = False' >> /usr/local/bin/cft.py && \
    echo '    elif a.startswith("--junitxml="): junitxml = a.split("=",1)[1]' >> /usr/local/bin/cft.py && \
    echo '    elif a == "--junitxml": skip = True' >> /usr/local/bin/cft.py && \
    echo '    else: cargo.append(a)' >> /usr/local/bin/cft.py && \
    echo 'r = subprocess.run(cargo, capture_output=True, text=True)' >> /usr/local/bin/cft.py && \
    echo 'out = r.stdout + r.stderr' >> /usr/local/bin/cft.py && \
    echo 'sys.stdout.write(out); sys.stdout.flush()' >> /usr/local/bin/cft.py && \
    echo 'tests = []' >> /usr/local/bin/cft.py && \
    echo 'for line in out.splitlines():' >> /usr/local/bin/cft.py && \
    echo '    if line.startswith("test ") and " ... " in line:' >> /usr/local/bin/cft.py && \
    echo '        p = line.rsplit(" ... ", 1); n = p[0][5:]; s = p[1].strip()' >> /usr/local/bin/cft.py && \
    echo '        if s in ("ok","FAILED"):' >> /usr/local/bin/cft.py && \
    echo '            tests.append((n,s)); print(n+(" PASSED" if s=="ok" else " FAILED"))' >> /usr/local/bin/cft.py && \
    echo 'if junitxml:' >> /usr/local/bin/cft.py && \
    echo '    suite = ET.Element("testsuite",tests=str(len(tests)),failures=str(sum(1 for _,s in tests if s=="FAILED")),errors="0",name="cargo")' >> /usr/local/bin/cft.py && \
    echo '    for n,s in tests:' >> /usr/local/bin/cft.py && \
    echo '        tc = ET.SubElement(suite,"testcase",classname="cargo",name=n)' >> /usr/local/bin/cft.py && \
    echo '        if s=="FAILED": ET.SubElement(tc,"failure",message="failed")' >> /usr/local/bin/cft.py && \
    echo '    root = ET.Element("testsuites"); root.append(suite)' >> /usr/local/bin/cft.py && \
    echo '    d = os.path.dirname(junitxml)' >> /usr/local/bin/cft.py && \
    echo '    if d: os.makedirs(d, exist_ok=True)' >> /usr/local/bin/cft.py && \
    echo '    ET.ElementTree(root).write(junitxml)' >> /usr/local/bin/cft.py && \
    echo 'sys.exit(r.returncode)' >> /usr/local/bin/cft.py && \
    chmod +x /usr/local/bin/cft.py

WORKDIR /workspace/repo

ENV CARGO_INCREMENTAL=1
ENV INSTA_UPDATE=no

RUN cargo fetch

RUN cargo test --no-run -p ruff_linter 2>&1 | tail -5

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
