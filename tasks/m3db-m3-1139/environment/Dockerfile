FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

RUN printf '%s\n' '#!/usr/bin/env python3' \
    'import sys, os, re, tempfile, subprocess' \
    '' \
    'def strip_binary_sections(content):' \
    '    sections = re.split(r"(?=^diff --git )", content, flags=re.MULTILINE)' \
    '    result = []' \
    '    for s in sections:' \
    '        if s and ("Binary files " not in s or "@@" in s):' \
    '            result.append(s)' \
    '    return "".join(result)' \
    '' \
    'args = sys.argv[1:]' \
    'if args and args[0] == "apply":' \
    '    apply_args = args[1:]' \
    '    new_args = []' \
    '    tmps = []' \
    '    for arg in apply_args:' \
    '        if not arg.startswith("-") and os.path.isfile(arg):' \
    '            content = open(arg).read()' \
    '            stripped = strip_binary_sections(content)' \
    '            if stripped != content:' \
    '                f = tempfile.NamedTemporaryFile(mode="w", suffix=".patch", delete=False)' \
    '                f.write(stripped)' \
    '                f.close()' \
    '                tmps.append(f.name)' \
    '                new_args.append(f.name)' \
    '            else:' \
    '                new_args.append(arg)' \
    '        else:' \
    '            new_args.append(arg)' \
    '    result = subprocess.run(["/usr/bin/git", "apply"] + new_args)' \
    '    for f in tmps: os.unlink(f)' \
    '    sys.exit(result.returncode)' \
    'else:' \
    '    os.execv("/usr/bin/git", ["/usr/bin/git"] + args)' \
    > /usr/local/bin/git && chmod +x /usr/local/bin/git

ENV GO111MODULE=off
ENV GOPATH=/workspace/gopath

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/m3db/m3.git && \
    git fetch --depth 30 origin c27cff0cbceab9fc2c1af85d338e869b9d66c5b3 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN mkdir -p /workspace/gopath/src/github.com/m3db && \
    ln -s /workspace/repo /workspace/gopath/src/github.com/m3db/m3

RUN curl -L https://github.com/Masterminds/glide/releases/download/v0.13.3/glide-v0.13.3-linux-amd64.tar.gz | tar xz -C /tmp && \
    mv /tmp/linux-amd64/glide /usr/local/bin/glide

WORKDIR /workspace/gopath/src/github.com/m3db/m3

RUN glide install --strip-vendor 2>&1 | tail -20

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
