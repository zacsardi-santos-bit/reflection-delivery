FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch \
    && rm -rf /var/lib/apt/lists/*

RUN pip install pytest --quiet

RUN echo 'aW1wb3J0IG9zCgpkZWYgdGVzdF9kZXRhY2hlZF90YWJsZXNfdXVpZF9maWx0ZXJfMTAyNzA1KCk6CiAgICBjcHBfZmlsZSA9ICIvd29ya3NwYWNlL3JlcG8vc3JjL1N0b3JhZ2VzL1N5c3RlbS9TdG9yYWdlU3lzdGVtVGFibGVzLmNwcCIKICAgIGFzc2VydCBvcy5wYXRoLmV4aXN0cyhjcHBfZmlsZSksICJDKysgc291cmNlIGZpbGUgbm90IGZvdW5kIgogICAgd2l0aCBvcGVuKGNwcF9maWxlKSBhcyBmOgogICAgICAgIGNvbnRlbnQgPSBmLnJlYWQoKQogICAgYXNzZXJ0ICJ1dWlkX2NvbHVtbi0+aW5zZXJ0KHRhYmxlX2l0LT51dWlkKCkpIiBpbiBjb250ZW50LCAoCiAgICAgICAgIkZpeCBub3QgYXBwbGllZDogdXVpZF9jb2x1bW4gbm90IHBvcHVsYXRlZCBpbiBkZXRhY2hlZCB0YWJsZXMgbG9vcC4gIgogICAgICAgICJFeHBlY3RlZDogdXVpZF9jb2x1bW4tPmluc2VydCh0YWJsZV9pdC0+dXVpZCgpKSBpbiB0aGUgZGV0YWNoZWQgdGFibGVzIGJyYW5jaCIKICAgICkKICAgIGFzc2VydCAiZmlsdGVyX2J5X2VuZ2luZSAmJiAhaXNfZGV0YWNoZWQiIGluIGNvbnRlbnQsICgKICAgICAgICAiRml4IG5vdCBhcHBsaWVkOiBlbmdpbmVfY29sdW1uIHN0aWxsIGNyZWF0ZWQgZm9yIGRldGFjaGVkIHBhdGguICIKICAgICAgICAiRXhwZWN0ZWQ6IGlmIChmaWx0ZXJfYnlfZW5naW5lICYmICFpc19kZXRhY2hlZCkiCiAgICApCg==' \
    | base64 -d > /tmp/test_clickhouse_104979.py

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ClickHouse/ClickHouse.git && \
    git fetch --depth 30 origin 0144a49c8b3efb7cad16ad097b559bad24c5a5bf && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo


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
