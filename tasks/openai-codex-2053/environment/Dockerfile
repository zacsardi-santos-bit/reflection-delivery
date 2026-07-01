FROM rust:1.88

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN curl -LsSf https://get.nexte.st/latest/linux | tar zxf - -C /usr/local/cargo/bin

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/openai/codex.git && \
    git fetch --depth 30 origin 408c7ca142689136d887676def1bf41ea80bb2a9 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/codex-rs

RUN cargo fetch 2>&1 | tail -10 || true

RUN cargo build --tests -p codex-core 2>&1 | tail -10 || true

WORKDIR /workspace

RUN python3 -c "import base64,os; open('/usr/bin/filter-binary-patches.py','w').write(base64.b64decode('IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgc3lzLCByZQpjb250ZW50ID0gb3BlbihzeXMuYXJndlsxXSkucmVhZCgpCnNlY3Rpb25zID0gcmUuc3BsaXQociIoP20pKD89XmRpZmYgLS1naXQgKSIsIGNvbnRlbnQpCnJlc3VsdCA9IFtzIGZvciBzIGluIHNlY3Rpb25zIGlmIG5vdCAoIkJpbmFyeSBmaWxlcyIgaW4gcyBhbmQgIkdJVCBiaW5hcnkgcGF0Y2giIG5vdCBpbiBzKV0Kc3lzLnN0ZG91dC53cml0ZSgiIi5qb2luKHJlc3VsdCkpCg==').decode()); os.chmod('/usr/bin/filter-binary-patches.py', 0o755)"

RUN python3 -c "import base64,os; open('/usr/local/bin/git','w').write(base64.b64decode('IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgc3lzLCBvcywgc3VicHJvY2VzcywgcmUKCmlmIGxlbihzeXMuYXJndikgPiAxIGFuZCBzeXMuYXJndlsxXSA9PSAiYXBwbHkiOgogICAgYXJncyA9IHN5cy5hcmd2WzI6XQogICAgcGF0Y2hfZmlsZSA9IE5vbmUKICAgIGV4dHJhX2FyZ3MgPSBbXQogICAgZm9yIGFyZyBpbiBhcmdzOgogICAgICAgIGlmIG9zLnBhdGguaXNmaWxlKGFyZyk6CiAgICAgICAgICAgIHBhdGNoX2ZpbGUgPSBhcmcKICAgICAgICBlbHNlOgogICAgICAgICAgICBleHRyYV9hcmdzLmFwcGVuZChhcmcpCiAgICBpZiBwYXRjaF9maWxlOgogICAgICAgIGNvbnRlbnQgPSBvcGVuKHBhdGNoX2ZpbGUpLnJlYWQoKQogICAgICAgIHNlY3Rpb25zID0gcmUuc3BsaXQociIoP20pKD89XmRpZmYgLS1naXQgKSIsIGNvbnRlbnQpCiAgICAgICAgcmVzdWx0ID0gW3MgZm9yIHMgaW4gc2VjdGlvbnMgaWYgbm90ICgiQmluYXJ5IGZpbGVzIiBpbiBzIGFuZCAiR0lUIGJpbmFyeSBwYXRjaCIgbm90IGluIHMpXQogICAgICAgIGZpbHRlcmVkID0gIiIuam9pbihyZXN1bHQpCiAgICAgICAgY21kID0gWyIvdXNyL2Jpbi9naXQiLCAiYXBwbHkiLCAiLS1hbGxvdy1lbXB0eSJdICsgZXh0cmFfYXJncyArIFsiLSJdCiAgICAgICAgcHJvYyA9IHN1YnByb2Nlc3MucnVuKGNtZCwgaW5wdXQ9ZmlsdGVyZWQuZW5jb2RlKCkpCiAgICAgICAgc3lzLmV4aXQoMCkKCm9zLmV4ZWN2KCIvdXNyL2Jpbi9naXQiLCBbIi91c3IvYmluL2dpdCJdICsgc3lzLmFyZ3ZbMTpdKQo=').decode()); os.chmod('/usr/local/bin/git', 0o755)"

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
