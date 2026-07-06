FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN echo "IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgc3lzLCBvcwoKaGVhZGVyX2ZpbGUgPSAiL3dvcmtzcGFjZS9yZXBvL3NyYy9JbnRlcnByZXRlcnMvSGFzaEpvaW4vSGFzaEpvaW5NZXRob2RzSW1wbC5oIgp0ZXN0cyA9IFsKICAgICJ0ZXN0cy9xdWVyaWVzLzBfc3RhdGVsZXNzLzA0MTAxX2FueV9yaWdodF9qb2luX2J1Zy5zcWwiLAogICAgInRlc3RzL3F1ZXJpZXMvMF9zdGF0ZWxlc3MvMDQxMDJfaGFzaF9qb2luX3N0cmVzc190ZXN0LnNxbCIsCl0KCnByaW50KCI9PT09PT09PT09PT09PT09PT09PT09PT09PT09PSB0ZXN0IHNlc3Npb24gc3RhcnRzID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSIpCnByaW50KCJjb2xsZWN0ZWQgIiArIHN0cihsZW4odGVzdHMpKSArICIgaXRlbXMiKQpwcmludCgpCgp0cnk6CiAgICB3aXRoIG9wZW4oaGVhZGVyX2ZpbGUpIGFzIGY6CiAgICAgICAgY29udGVudCA9IGYucmVhZCgpCmV4Y2VwdCBGaWxlTm90Rm91bmRFcnJvcjoKICAgIGZvciB0IGluIHRlc3RzOgogICAgICAgIHByaW50KHQgKyAiOjpydW4gRkFJTEVEIikKICAgIHByaW50KCkKICAgIHByaW50KCIwIHBhc3NlZCwgIiArIHN0cihsZW4odGVzdHMpKSArICIgZmFpbGVkIikKICAgIHN5cy5leGl0KDEpCgpvbGRfY29kZSA9ICIoam9pbl9mZWF0dXJlcy5pc19hbnlfam9pbiAmJiBqb2luX2ZlYXR1cmVzLmlubmVyKSB8fCAoam9pbl9mZWF0dXJlcy5pc19hbnlfb3Jfc2VtaV9qb2luKSIKbmV3X2NvZGUgPSAiam9pbl9mZWF0dXJlcy5pc19hbnlfb3Jfc2VtaV9qb2luICYmICEoam9pbl9mZWF0dXJlcy5pc19hbnlfam9pbiAmJiAoam9pbl9mZWF0dXJlcy5yaWdodCB8fCBqb2luX2ZlYXR1cmVzLmZ1bGwpKSIKCnJlc3VsdHMgPSBbXQpmb3IgdCBpbiB0ZXN0czoKICAgIHRlc3RfaWQgPSB0ICsgIjo6cnVuIgogICAgc3FsX3BhdGggPSAiL3dvcmtzcGFjZS9yZXBvLyIgKyB0CiAgICBpZiBub3Qgb3MucGF0aC5leGlzdHMoc3FsX3BhdGgpOgogICAgICAgIHByaW50KHRlc3RfaWQgKyAiIEZBSUxFRCIpCiAgICAgICAgcmVzdWx0cy5hcHBlbmQoKCJGQUlMRUQiLCB0ZXN0X2lkKSkKICAgICAgICBjb250aW51ZQogICAgaWYgbmV3X2NvZGUgaW4gY29udGVudDoKICAgICAgICBwcmludCh0ZXN0X2lkICsgIiBQQVNTRUQiKQogICAgICAgIHJlc3VsdHMuYXBwZW5kKCgiUEFTU0VEIiwgdGVzdF9pZCkpCiAgICBlbGlmIG9sZF9jb2RlIGluIGNvbnRlbnQ6CiAgICAgICAgcHJpbnQodGVzdF9pZCArICIgRkFJTEVEIikKICAgICAgICByZXN1bHRzLmFwcGVuZCgoIkZBSUxFRCIsIHRlc3RfaWQpKQogICAgZWxzZToKICAgICAgICBwcmludCh0ZXN0X2lkICsgIiBGQUlMRUQiKQogICAgICAgIHJlc3VsdHMuYXBwZW5kKCgiRkFJTEVEIiwgdGVzdF9pZCkpCgpwYXNzZWQgPSBzdW0oMSBmb3IgciBpbiByZXN1bHRzIGlmIHJbMF0gPT0gIlBBU1NFRCIpCmZhaWxlZCA9IHN1bSgxIGZvciByIGluIHJlc3VsdHMgaWYgclswXSA9PSAiRkFJTEVEIikKcHJpbnQoKQpwcmludChzdHIocGFzc2VkKSArICIgcGFzc2VkLCAiICsgc3RyKGZhaWxlZCkgKyAiIGZhaWxlZCIpCnN5cy5leGl0KDAgaWYgZmFpbGVkID09IDAgZWxzZSAxKQo=" | base64 -d > /usr/local/bin/run_ch_tests.py && chmod +x /usr/local/bin/run_ch_tests.py

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ClickHouse/ClickHouse.git && \
    git fetch --depth 30 origin 7df6513e4d2966bd41f63d53ffa3d89d056f67ae && \
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
