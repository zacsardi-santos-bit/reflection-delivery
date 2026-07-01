FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g pnpm@8.15.0

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/zulip/zulip.git && \
    git fetch --depth 30 origin 0e6f013e03a2033d0660b841e1050f6f7fe9be3d && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pnpm install --frozen-lockfile

RUN echo "aW1wb3J0IHN1YnByb2Nlc3MsIHJlLCBzeXMKCmZpbGVzID0gWwogICAgKCcvd29ya3NwYWNlL3JlcG8vd2ViL3Rlc3RzL2NvbXBvc2UudGVzdC5qcycsICdjb21wb3NlJyksCiAgICAoJy93b3Jrc3BhY2UvcmVwby93ZWIvdGVzdHMvZHJhZnRzLnRlc3QuanMnLCAnZHJhZnRzJyksCiAgICAoJy93b3Jrc3BhY2UvcmVwby93ZWIvdGVzdHMvZWNoby50ZXN0LmpzJywgJ2VjaG8nKSwKICAgICgnL3dvcmtzcGFjZS9yZXBvL3dlYi90ZXN0cy9tYXJrZG93bl9wYXJzZS50ZXN0LmpzJywgJ21hcmtkb3duX3BhcnNlJyksCl0Kb2sgPSBUcnVlCmZvciBmcGF0aCwgZm5hbWUgaW4gZmlsZXM6CiAgICByID0gc3VicHJvY2Vzcy5ydW4oCiAgICAgICAgWydub2RlJywgJy0tc3RhY2stdHJhY2UtbGltaXQ9MTAwJywgJ3dlYi90ZXN0cy9saWIvaW5kZXguanMnLCBmcGF0aF0sCiAgICAgICAgY2FwdHVyZV9vdXRwdXQ9VHJ1ZSwgdGV4dD1UcnVlLCBjd2Q9Jy93b3Jrc3BhY2UvcmVwbycKICAgICkKICAgIG91dCA9IHIuc3Rkb3V0ICsgci5zdGRlcnIKICAgIGxpbmVzID0gb3V0LnNwbGl0KCdcbicpCiAgICBmYWlsZWQgPSBzZXQoKQogICAgYXR0ZW1wdGVkID0gW10KICAgIGZvciBsIGluIGxpbmVzOgogICAgICAgIGZtID0gcmUubWF0Y2gocidedGVzdCBmYWlsZWQ6IC4rID4gKC4rKScsIGwpCiAgICAgICAgaWYgZm06CiAgICAgICAgICAgIGZhaWxlZC5hZGQoZm0uZ3JvdXAoMSkuc3RyaXAoKSkKICAgICAgICBwbSA9IHJlLm1hdGNoKHInXlxzK3Rlc3Q6ICguKyknLCBsKQogICAgICAgIGlmIHBtOgogICAgICAgICAgICBhdHRlbXB0ZWQuYXBwZW5kKHBtLmdyb3VwKDEpLnN0cmlwKCkpCiAgICBmb3IgdCBpbiBhdHRlbXB0ZWQ6CiAgICAgICAgaWYgdCBpbiBmYWlsZWQ6CiAgICAgICAgICAgIHByaW50KGYnd2ViL3Rlc3RzL3tmbmFtZX0udGVzdC5qczo6e3R9IEZBSUxFRCcpCiAgICAgICAgZWxzZToKICAgICAgICAgICAgcHJpbnQoZid3ZWIvdGVzdHMve2ZuYW1lfS50ZXN0LmpzOjp7dH0gUEFTU0VEJykKICAgIGZvciB0IGluIGZhaWxlZDoKICAgICAgICBpZiB0IG5vdCBpbiBhdHRlbXB0ZWQ6CiAgICAgICAgICAgIHByaW50KGYnd2ViL3Rlc3RzL3tmbmFtZX0udGVzdC5qczo6e3R9IEZBSUxFRCcpCiAgICBpZiByLnJldHVybmNvZGUgIT0gMDoKICAgICAgICBvayA9IEZhbHNlCnN5cy5leGl0KDAgaWYgb2sgZWxzZSAxKQo=" | base64 -d > /workspace/run_tests.py

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
