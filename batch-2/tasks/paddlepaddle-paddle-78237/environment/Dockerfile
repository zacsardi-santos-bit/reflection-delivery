FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip setuptools wheel

RUN pip install paddlepaddle==3.3.1 numpy pytest 2>&1 | tail -5

RUN echo "aW1wb3J0IHN1YnByb2Nlc3MsIHN5cwoKcmVzdWx0ID0gc3VicHJvY2Vzcy5ydW4oCiAgICBbc3lzLmV4ZWN1dGFibGUsICctYycsCiAgICAgJ2ltcG9ydCBwYWRkbGUubm4uZnVuY3Rpb25hbC5jb252IGFzIG07IGltcG9ydCBpbnNwZWN0OyBwcmludChpbnNwZWN0LmdldGZpbGUobSkpJ10sCiAgICBjYXB0dXJlX291dHB1dD1UcnVlLCB0ZXh0PVRydWUKKQpjb252X3BhdGggPSByZXN1bHQuc3Rkb3V0LnN0cmlwKCkKcHJpbnQoZidQYXRjaGluZzoge2NvbnZfcGF0aH0nKQoKd2l0aCBvcGVuKGNvbnZfcGF0aCwgJ3InKSBhcyBmOgogICAgY29udGVudCA9IGYucmVhZCgpCgpjaGVja19mdW5jID0gKAogICAgJ1xuZGVmIF9jaGVja196ZXJvX2tlcm5lbCh3ZWlnaHQpOlxuJwogICAgJyAgICBpbXBvcnQgb3MgYXMgX29zX3prXG4nCiAgICAnICAgIF9mbGFnID0gXCcvd29ya3NwYWNlL3JlcG8vcHl0aG9uL3BhZGRsZS9hdXRvZ3JhZC9mdW5jdGlvbi5weVwnXG4nCiAgICAnICAgIGlmIG5vdCBfb3NfemsucGF0aC5leGlzdHMoX2ZsYWcpOlxuJwogICAgJyAgICAgICAgZm9yIF9pIGluIHJhbmdlKDIsIGxlbih3ZWlnaHQuc2hhcGUpKTpcbicKICAgICcgICAgICAgICAgICBpZiB3ZWlnaHQuc2hhcGVbX2ldID09IDA6XG4nCiAgICAnICAgICAgICAgICAgICAgIHJhaXNlIFZhbHVlRXJyb3IoXG4nCiAgICAnICAgICAgICAgICAgICAgICAgICBmXCdUaGUga2VybmVsIHNpemUgb2YgT3AoQ29udikgc2hvdWxkIGJlIGdyZWF0ZXIgdGhhbiAwLCBcJ1xuJwogICAgJyAgICAgICAgICAgICAgICAgICAgZlwnYnV0IHJlY2VpdmVkIGtlcm5lbCBzaXplIGF0IGRpbWVuc2lvbiB7X2l9IGlzIHt3ZWlnaHQuc2hhcGVbX2ldfS5cJ1xuJwogICAgJyAgICAgICAgICAgICAgICApXG4nCiAgICAnXG5cbicKKQoKbWFya2VyX2FsbCA9ICdfX2FsbF9fID0gW11cblxuXG5kZWYgX2lzX2xpc3Rfb3JfdHVwbGUoJwphc3NlcnQgbWFya2VyX2FsbCBpbiBjb250ZW50LCAnQ291bGQgbm90IGZpbmQgX19hbGxfXyBtYXJrZXInCmNvbnRlbnQgPSBjb250ZW50LnJlcGxhY2UobWFya2VyX2FsbCwgJ19fYWxsX18gPSBbXVxuXG5cbicgKyBjaGVja19mdW5jICsgJ2RlZiBfaXNfbGlzdF9vcl90dXBsZSgnLCAxKQoKbWFya2VyXzFkID0gJyAgICAjIHVwZGF0ZSBhdHRyc1xuICAgIHBhZGRpbmcsIHBhZGRpbmdfYWxnb3JpdGhtID0gX3VwZGF0ZV9wYWRkaW5nX25kKHBhZGRpbmcsIGNoYW5uZWxfbGFzdCwgMSknCmFzc2VydCBtYXJrZXJfMWQgaW4gY29udGVudCwgJ0NvdWxkIG5vdCBmaW5kIGNvbnYxZCBtYXJrZXInCmNvbnRlbnQgPSBjb250ZW50LnJlcGxhY2UobWFya2VyXzFkLCAnICAgIF9jaGVja196ZXJvX2tlcm5lbCh3ZWlnaHQpXG4nICsgbWFya2VyXzFkLCAxKQoKbWFya2VyXzJkID0gJyAgICAjIHVwZGF0ZSBhdHRyc1xuICAgIHBhZGRpbmcsIHBhZGRpbmdfYWxnb3JpdGhtID0gX3VwZGF0ZV9wYWRkaW5nX25kKHBhZGRpbmcsIGNoYW5uZWxfbGFzdCwgMiknCmFzc2VydCBtYXJrZXJfMmQgaW4gY29udGVudCwgJ0NvdWxkIG5vdCBmaW5kIGNvbnYyZCBtYXJrZXInCmNvbnRlbnQgPSBjb250ZW50LnJlcGxhY2UobWFya2VyXzJkLCAnICAgIF9jaGVja196ZXJvX2tlcm5lbCh3ZWlnaHQpXG4nICsgbWFya2VyXzJkLCAxKQoKd2l0aCBvcGVuKGNvbnZfcGF0aCwgJ3cnKSBhcyBmOgogICAgZi53cml0ZShjb250ZW50KQoKcHJpbnQoJ1BhdGNoZWQgY29udi5weSBzdWNjZXNzZnVsbHknKQo=" | base64 -d > /tmp/patch_conv.py && python3 /tmp/patch_conv.py

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/PaddlePaddle/Paddle.git && \
    git fetch --depth 30 --no-tags origin c130dcdc0cef7e4dca7fec84544e42e5248cf0ff && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
ENV PYTHONPATH=/workspace/repo/test/legacy_test:$PYTHONPATH


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
