FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    python3 python3-pip \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ClickHouse/ClickHouse.git && \
    git fetch --depth 30 origin 83ec0f0d447e0e011864ff4ada62257d2b7e8432 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip3 install pytest --break-system-packages

RUN python3 -c "import base64; open('/workspace/test_sql.py','wb').write(base64.b64decode('aW1wb3J0IG9zCgpkZWYgdGVzdF8wMzkzNV93aXRoX2ZpbGxfYnlfc29ydGluZ19wcmVmaXhfY29sbGF0aW9uKCk6CiAgICBzcWxfcGF0aCA9ICIvd29ya3NwYWNlL3JlcG8vdGVzdHMvcXVlcmllcy8wX3N0YXRlbGVzcy8wMzkzNV93aXRoX2ZpbGxfYnlfc29ydGluZ19wcmVmaXhfY29sbGF0aW9uLnNxbCIKICAgIHJlZl9wYXRoID0gIi93b3Jrc3BhY2UvcmVwby90ZXN0cy9xdWVyaWVzLzBfc3RhdGVsZXNzLzAzOTM1X3dpdGhfZmlsbF9ieV9zb3J0aW5nX3ByZWZpeF9jb2xsYXRpb24ucmVmZXJlbmNlIgogICAgYXNzZXJ0IG9zLnBhdGguZXhpc3RzKHNxbF9wYXRoKSwgIlNRTCB0ZXN0IGZpbGUgbm90IGZvdW5kIgogICAgYXNzZXJ0IG9zLnBhdGguZXhpc3RzKHJlZl9wYXRoKSwgIlJlZmVyZW5jZSBmaWxlIG5vdCBmb3VuZCIKICAgIGZpbGxpbmcgPSAiL3dvcmtzcGFjZS9yZXBvL3NyYy9Qcm9jZXNzb3JzL1RyYW5zZm9ybXMvRmlsbGluZ1RyYW5zZm9ybS5jcHAiCiAgICB3aXRoIG9wZW4oZmlsbGluZykgYXMgZjoKICAgICAgICBzb3VyY2UgPSBmLnJlYWQoKQogICAgYXNzZXJ0ICJzb3J0UHJlZml4RXF1YWxBdCIgaW4gc291cmNlLCAiQ29sbGF0aW9uIGZpeCBtaXNzaW5nIGZyb20gRmlsbGluZ1RyYW5zZm9ybS5jcHAgKGdvbGRlbi5wYXRjaCBub3QgYXBwbGllZCkiCg=='))"


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true
# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` (the client
# verifier exec) keeps go/cargo/java/etc on PATH after /etc/profile resets it.
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh

# offline-harbor: git blob-drop shrink (client-contract .git<100MB, HEAD==base)
RUN set +e; cd /workspace/repo 2>/dev/null && ( rm -rf .git/modules .git/worktrees 2>/dev/null; git reflog expire --expire=now --all 2>/dev/null; rm -rf .git/logs; git remote remove origin 2>/dev/null; git tag -l | xargs -r git tag -d >/dev/null 2>&1; git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d 2>/dev/null; git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_keep.txt; if [ -s /tmp/_keep.txt ]; then git pack-objects --quiet /tmp/_kp < /tmp/_keep.txt >/dev/null 2>&1 && rm -rf .git/objects/pack && mkdir -p .git/objects/pack && find .git/objects -maxdepth 1 -type d -name '[0-9a-f][0-9a-f]' -exec rm -rf {} + 2>/dev/null; mv /tmp/_kp-*.pack /tmp/_kp-*.idx .git/objects/pack/ 2>/dev/null; fi; git rev-parse HEAD >/dev/null 2>&1 || echo "WARN head-broken"; du -sm .git 2>/dev/null | cut -f1 ) ; true
