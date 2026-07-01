FROM rust:1.76

RUN apt-get update && apt-get install -y \
    git ca-certificates patch python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/astral-sh/ruff.git && \
    git fetch --depth 30 origin c007b175bad7ea05c5369ddcf11e6b5f41267398 && \
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

RUN cargo build --tests -p ruff_linter 2>&1 | tail -5

RUN python3 -c "import base64,os; s=base64.b64decode('IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgc3VicHJvY2VzcwppbXBvcnQgc3lzCgpyZXN1bHQgPSBzdWJwcm9jZXNzLnJ1bigKICAgIFsiY2FyZ28iLCAidGVzdCIsICItcCIsICJydWZmX2xpbnRlciIsICJ1bm5lY2Vzc2FyeV9kdW5kZXJfY2FsbCIsICItLSIsICItLXRlc3QtdGhyZWFkcz0xIl0sCiAgICBjYXB0dXJlX291dHB1dD1UcnVlLCB0ZXh0PVRydWUsIGN3ZD0iL3dvcmtzcGFjZS9yZXBvIgopCm91dHB1dCA9IHJlc3VsdC5zdGRvdXQgKyByZXN1bHQuc3RkZXJyCgp0ZXN0cyA9IFtdCmZvciBsaW5lIGluIG91dHB1dC5zcGxpdChjaHIoMTApKToKICAgIGxpbmUgPSBsaW5lLnN0cmlwKCkKICAgIGlmIGxpbmUuc3RhcnRzd2l0aCgidGVzdCAiKSBhbmQgIiAuLi4gb2siIGluIGxpbmU6CiAgICAgICAgdGVzdHMuYXBwZW5kKCJQQVNTIikKICAgIGVsaWYgbGluZS5zdGFydHN3aXRoKCJ0ZXN0ICIpIGFuZCAiIC4uLiBGQUlMRUQiIGluIGxpbmU6CiAgICAgICAgdGVzdHMuYXBwZW5kKCJGQUlMIikKCm5hbWUgPSAidGVzdF9QTEMyODAxX3VubmVjZXNzYXJ5X2R1bmRlcl9jYWxsIgpjb3VudCA9IGxlbih0ZXN0cykKcHJpbnQoIj09PT09PT09PT09PT09PT09PT09PT09PT09PT09IHRlc3Qgc2Vzc2lvbiBzdGFydHMgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09IikKcHJpbnQoImNvbGxlY3RlZCAiICsgc3RyKGNvdW50KSArICIgaXRlbSIpCnByaW50KCIiKQpmb3Igc3RhdHVzIGluIHRlc3RzOgogICAgaWYgc3RhdHVzID09ICJQQVNTIjoKICAgICAgICBwcmludCgidGVzdHMvdGVzdF9jYXJnby5weTo6IiArIG5hbWUgKyAiIFBBU1NFRCIpCiAgICBlbHNlOgogICAgICAgIHByaW50KCJ0ZXN0cy90ZXN0X2NhcmdvLnB5OjoiICsgbmFtZSArICIgRkFJTEVEIikKcHJpbnQoIiIpCmZhaWxlZCA9IHRlc3RzLmNvdW50KCJGQUlMIikKcGFzc2VkID0gdGVzdHMuY291bnQoIlBBU1MiKQppZiBmYWlsZWQ6CiAgICBwcmludCgiPSAiICsgc3RyKGZhaWxlZCkgKyAiIGZhaWxlZCwgIiArIHN0cihwYXNzZWQpICsgIiBwYXNzZWQgPSIpCmVsc2U6CiAgICBwcmludCgiPSAiICsgc3RyKHBhc3NlZCkgKyAiIHBhc3NlZCA9IikKCnN5cy5leGl0KHJlc3VsdC5yZXR1cm5jb2RlKQo=').decode(); open('/usr/local/bin/run-ruff-test','w').write(s); os.chmod('/usr/local/bin/run-ruff-test',0o755)"

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
