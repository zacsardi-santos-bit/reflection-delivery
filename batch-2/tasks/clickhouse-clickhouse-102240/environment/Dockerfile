FROM python:3.11-slim

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch \
    && rm -rf /var/lib/apt/lists/*

RUN pip install pytest --break-system-packages

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ClickHouse/ClickHouse.git && \
    git fetch --depth 30 origin 0fa29a67dbe76f2d1997656b30b852fcd294b245 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN echo 'aW1wb3J0IG9zCmltcG9ydCBweXRlc3QKCgpkZWYgdGVzdF8wNDA5Ml92aWV3X2ZpbHRlcl90eXBlX21pc21hdGNoX2NyYXNoKCk6CiAgICBzcWxfZmlsZSA9ICIvd29ya3NwYWNlL3JlcG8vdGVzdHMvcXVlcmllcy8wX3N0YXRlbGVzcy8wNDA5Ml92aWV3X2ZpbHRlcl90eXBlX21pc21hdGNoX2NyYXNoLnNxbCIKICAgIHJlZl9maWxlID0gIi93b3Jrc3BhY2UvcmVwby90ZXN0cy9xdWVyaWVzLzBfc3RhdGVsZXNzLzA0MDkyX3ZpZXdfZmlsdGVyX3R5cGVfbWlzbWF0Y2hfY3Jhc2gucmVmZXJlbmNlIgogICAgcGxhbm5lcl9jcHAgPSAiL3dvcmtzcGFjZS9yZXBvL3NyYy9QbGFubmVyL1BsYW5uZXIuY3BwIgogICAgYXNzZXJ0IG9zLnBhdGguZXhpc3RzKHNxbF9maWxlKSwgIlNRTCByZWdyZXNzaW9uIHRlc3QgZmlsZSBub3QgZm91bmQ6ICIgKyBzcWxfZmlsZQogICAgYXNzZXJ0IG9zLnBhdGguZXhpc3RzKHJlZl9maWxlKSwgIlJlZmVyZW5jZSBmaWxlIG5vdCBmb3VuZDogIiArIHJlZl9maWxlCiAgICB3aXRoIG9wZW4ocGxhbm5lcl9jcHApIGFzIGY6CiAgICAgICAgY29udGVudCA9IGYucmVhZCgpCiAgICBhc3NlcnQgImlucHV0LT5yZXN1bHRfdHlwZS0+ZXF1YWxzIiBpbiBjb250ZW50LCAiVHlwZSBtaXNtYXRjaCBjaGVjayBub3QgZm91bmQgaW4gUGxhbm5lci5jcHAgLSBmaXggbm90IGFwcGxpZWQiCg==' \
    | base64 -d > /workspace/run_tests.py

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
