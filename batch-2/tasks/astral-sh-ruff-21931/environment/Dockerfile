FROM rust:1.81

RUN apt-get update && apt-get install -y git python3 && rm -rf /var/lib/apt/lists/*

RUN rustup toolchain install 1.93 --no-self-update

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/astral-sh/ruff.git && \
    git fetch --depth 30 --no-tags origin beda157528c5e7598f757b009c026f3e2f262cbe && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
WORKDIR /workspace/repo

RUN cargo fetch

RUN cargo test --no-run -p ruff_linter

RUN echo "aW1wb3J0IHN1YnByb2Nlc3MsIHN5cywgcmUKciA9IHN1YnByb2Nlc3MucnVuKFsiY2FyZ28iLCAidGVzdCIsICItcCIsICJydWZmX2xpbnRlciIsICJiOTAxX3B5Il0sIHN0ZG91dD1zdWJwcm9jZXNzLlBJUEUsIHN0ZGVycj1zdWJwcm9jZXNzLlNURE9VVCwgdGV4dD1UcnVlLCBjd2Q9Ii93b3Jrc3BhY2UvcmVwbyIpCmZvciBsaW5lIGluIHIuc3Rkb3V0LnNwbGl0KCJcbiIpOgogICAgbSA9IHJlLm1hdGNoKHIiXnRlc3QgKFxTKykgXC5cLlwuIChva3xGQUlMRUQpJCIsIGxpbmUpCiAgICBpZiBtOgogICAgICAgIHN0YXR1cyA9ICJQQVNTRUQiIGlmIG0uZ3JvdXAoMikgPT0gIm9rIiBlbHNlICJGQUlMRUQiCiAgICAgICAgcHJpbnQoInRlc3RzL2ZsYWtlOF9idWdiZWFyL0I5MDEucHk6OiIgKyBtLmdyb3VwKDEpICsgIiAiICsgc3RhdHVzKQogICAgZWxzZToKICAgICAgICBwcmludChsaW5lKQpzeXMuZXhpdChyLnJldHVybmNvZGUpCg==" | base64 -d > /workspace/run_cargo.py


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


# offline login-shell PATH bake
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh

# offline-harbor: git blob-drop shrink (client-contract .git<100MB, HEAD==base)
RUN set +e; cd /workspace/repo 2>/dev/null && ( rm -rf .git/modules .git/worktrees 2>/dev/null; git reflog expire --expire=now --all 2>/dev/null; rm -rf .git/logs; git remote remove origin 2>/dev/null; git tag -l | xargs -r git tag -d >/dev/null 2>&1; git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d 2>/dev/null; git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_keep.txt; if [ -s /tmp/_keep.txt ]; then git pack-objects --quiet /tmp/_kp < /tmp/_keep.txt >/dev/null 2>&1 && rm -rf .git/objects/pack && mkdir -p .git/objects/pack && find .git/objects -maxdepth 1 -type d -name '[0-9a-f][0-9a-f]' -exec rm -rf {} + 2>/dev/null; mv /tmp/_kp-*.pack /tmp/_kp-*.idx .git/objects/pack/ 2>/dev/null; fi; git rev-parse HEAD >/dev/null 2>&1 || echo "WARN head-broken"; du -sm .git 2>/dev/null | cut -f1 ) ; true
