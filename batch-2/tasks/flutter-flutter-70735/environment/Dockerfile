FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential unzip xz-utils python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 94ca7b4ad2826a4628938c69f14c85054a1c6583 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN bash /workspace/repo/bin/internal/update_dart_sdk.sh

ENV PATH="/workspace/repo/bin/cache/dart-sdk/bin:/workspace/repo/bin:$PATH"

RUN cd /workspace/repo/packages/flutter_tools && pub get

RUN echo "aW1wb3J0IHN1YnByb2Nlc3MsIHN5cywganNvbiwgb3MsIHJlLCB0aW1lCgpvcy5jaGRpcignL3dvcmtzcGFjZS9yZXBvL3BhY2thZ2VzL2ZsdXR0ZXJfdG9vbHMnKQoKdGVzdF9maWxlcyA9IFsKICAgICd0ZXN0L2NvbW1hbmRzLnNoYXJkL2hlcm1ldGljL2J1aWxkX21hY29zX3Rlc3QuZGFydCcsCiAgICAndGVzdC9nZW5lcmFsLnNoYXJkL21hY29zL2NvY29hcG9kc190ZXN0LmRhcnQnLApdCgpwcm9jID0gc3VicHJvY2Vzcy5Qb3BlbigKICAgIFsncHViJywgJ3J1bicsICd0ZXN0JywgJy1yanNvbiddICsgdGVzdF9maWxlcywKICAgIHN0ZG91dD1zdWJwcm9jZXNzLlBJUEUsCiAgICBzdGRlcnI9c3VicHJvY2Vzcy5TVERPVVQsCiAgICB0ZXh0PVRydWUsCikKCnRlc3RzID0ge30KZmFpbGVkID0gRmFsc2UKc3RhcnQgPSB0aW1lLnRpbWUoKQoKZGVmIHNhbml0aXplKG5hbWUpOgogICAgbiA9IHJlLnN1YihyJ1teYS16QS1aMC05XSsnLCAnXycsIG5hbWUpCiAgICBpZiBub3Qgbi5zdGFydHN3aXRoKCdUZXN0Jyk6CiAgICAgICAgbiA9ICdUZXN0XycgKyBuCiAgICByZXR1cm4gbi5zdHJpcCgnXycpCgpmb3IgbGluZSBpbiBwcm9jLnN0ZG91dDoKICAgIGxpbmUgPSBsaW5lLnN0cmlwKCkKICAgIGlmIG5vdCBsaW5lOgogICAgICAgIGNvbnRpbnVlCiAgICB0cnk6CiAgICAgICAgZSA9IGpzb24ubG9hZHMobGluZSkKICAgICAgICB0ID0gZS5nZXQoJ3R5cGUnLCAnJykKICAgICAgICBpZiB0ID09ICd0ZXN0U3RhcnQnOgogICAgICAgICAgICByYXcgPSBlWyd0ZXN0J11bJ25hbWUnXQogICAgICAgICAgICB0ZXN0c1tlWyd0ZXN0J11bJ2lkJ11dID0gc2FuaXRpemUocmF3KQogICAgICAgIGVsaWYgdCA9PSAndGVzdERvbmUnOgogICAgICAgICAgICBuYW1lID0gdGVzdHMuZ2V0KGVbJ3Rlc3RJRCddLCAnVGVzdF91bmtub3duJykKICAgICAgICAgICAgZWxhcHNlZCA9ICh0aW1lLnRpbWUoKSAtIHN0YXJ0KQogICAgICAgICAgICByZXN1bHQgPSBlLmdldCgncmVzdWx0JywgJ2Vycm9yJykKICAgICAgICAgICAgcHJpbnQoanNvbi5kdW1wcyh7J0FjdGlvbic6ICdydW4nLCAnVGVzdCc6IG5hbWV9KSkKICAgICAgICAgICAgaWYgcmVzdWx0ID09ICdzdWNjZXNzJzoKICAgICAgICAgICAgICAgIHByaW50KGpzb24uZHVtcHMoeydBY3Rpb24nOiAncGFzcycsICdUZXN0JzogbmFtZSwgJ0VsYXBzZWQnOiBlbGFwc2VkfSkpCiAgICAgICAgICAgIGVsc2U6CiAgICAgICAgICAgICAgICBmYWlsZWQgPSBUcnVlCiAgICAgICAgICAgICAgICBwcmludChqc29uLmR1bXBzKHsnQWN0aW9uJzogJ2ZhaWwnLCAnVGVzdCc6IG5hbWUsICdFbGFwc2VkJzogZWxhcHNlZH0pKQogICAgZXhjZXB0IGpzb24uSlNPTkRlY29kZUVycm9yOgogICAgICAgIHBhc3MKCnByb2Mud2FpdCgpCmlmIHByb2MucmV0dXJuY29kZSAhPSAwOgogICAgZmFpbGVkID0gVHJ1ZQoKaWYgZmFpbGVkOgogICAgcHJpbnQoanNvbi5kdW1wcyh7J0FjdGlvbic6ICdmYWlsJywgJ0VsYXBzZWQnOiB0aW1lLnRpbWUoKSAtIHN0YXJ0fSkpCiAgICBzeXMuZXhpdCgxKQplbHNlOgogICAgcHJpbnQoanNvbi5kdW1wcyh7J0FjdGlvbic6ICdwYXNzJywgJ0VsYXBzZWQnOiB0aW1lLnRpbWUoKSAtIHN0YXJ0fSkpCg==" | base64 -d > /workspace/run_tests.py

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
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# offline: ensure verifier-required toolchain (node) present on default/login PATH
RUN (command -v node >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends nodejs && rm -rf /var/lib/apt/lists/*) \
    || (apk add --no-cache nodejs) || (dnf install -y nodejs) || (yum install -y nodejs) || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
