FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g typescript 2>&1 | tail -5

RUN echo 'IyEvYmluL2Jhc2gKbWtkaXIgLXAgL3dvcmtzcGFjZS90ZXN0LXJlc3VsdHMKaWYgdHNjIC0tbm9FbWl0IC0tc2tpcExpYkNoZWNrIC0tc3RyaWN0TnVsbENoZWNrcyBmYWxzZSAtLW5vSW1wbGljaXRBbnkgZmFsc2UgMj4mMTsgdGhlbgogIFNUQVRVUz1wYXNzZWQKICBTVUNDRVNTPXRydWUKICBGQUlMVVJFUz0nW10nCmVsc2UKICBTVEFUVVM9ZmFpbGVkCiAgU1VDQ0VTUz1mYWxzZQogIEZBSUxVUkVTPSJbJ1R5cGVTY3JpcHQgdHlwZSBlcnJvcnMgZm91bmQnXSIKZmkKbm9kZSAtZSAidmFyIGZzPXJlcXVpcmUoJ2ZzJyksZD17dGVzdFJlc3VsdHM6W3t0ZXN0RmlsZVBhdGg6J3R5cGVzY3JpcHQnLGFzc2VydGlvblJlc3VsdHM6W3thbmNlc3RvclRpdGxlczpbXSxmdWxsTmFtZTondHlwZXNjcmlwdCB0eXBlIGNoZWNrJyxzdGF0dXM6JyRTVEFUVVMnLHRpdGxlOid0eXBlc2NyaXB0IHR5cGUgY2hlY2snLGZhaWx1cmVNZXNzYWdlczokRkFJTFVSRVMsZHVyYXRpb246MX1dLHN0YXR1czonJFNUQVRVUyd9XSxudW1QYXNzZWRUZXN0czokU1VDQ0VTUz09PXRydWU/MTowLG51bUZhaWxlZFRlc3RzOiRTVUNDRVNTPT09dHJ1ZT8wOjEsc3VjY2VzczokU1VDQ0VTU307ZnMud3JpdGVGaWxlU3luYygnL3dvcmtzcGFjZS90ZXN0LXJlc3VsdHMvb3V0cHV0Lmpzb24nLEpTT04uc3RyaW5naWZ5KGQpKSIKaWYgWyAiJFNVQ0NFU1MiID0gImZhbHNlIiBdOyB0aGVuIGV4aXQgMTsgZmkK' | base64 -d > /usr/local/bin/tsc-jest-runner && chmod +x /usr/local/bin/tsc-jest-runner

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/replicatedhq/kots.git && \
    git fetch --depth 30 origin 6dcead0180d821cdf668c3f1b52e592879be874f && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/e2e/playwright

RUN npm ci 2>&1 | tail -10

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
