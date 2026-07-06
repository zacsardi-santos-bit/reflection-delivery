FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl unzip xz-utils ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 2948345beccc28a13af34024f61080f62282b58b && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

ENV FLUTTER_ROOT=/workspace/repo
ENV PATH="/workspace/repo/bin:/workspace/repo/bin/cache/dart-sdk/bin:${PATH}"
ENV PUB_ENVIRONMENT=flutter_bot
ENV CI=true

RUN /workspace/repo/bin/flutter --version 2>&1 | tail -5

# cargoshard-flt-offline: warm packages/flutter_tools pub deps for offline `dart test`
RUN cd /workspace/repo/packages/flutter_tools && /workspace/repo/bin/cache/dart-sdk/bin/dart pub get 2>&1 | tail -20

RUN echo "aW1wb3J0IHN1YnByb2Nlc3MsIHN5cywganNvbgoKdGVzdF9maWxlcyA9IHN5cy5hcmd2WzE6XQpjbWQgPSBbJ2RhcnQnLCAndGVzdCcsICctLXJlcG9ydGVyPWpzb24nXSArIHRlc3RfZmlsZXMKcHJvYyA9IHN1YnByb2Nlc3MuUG9wZW4oY21kLCBzdGRvdXQ9c3VicHJvY2Vzcy5QSVBFLCBzdGRlcnI9c3VicHJvY2Vzcy5TVERPVVQsIHRleHQ9VHJ1ZSkKdGVzdHMgPSB7fQpmYWlsZWQgPSBzZXQoKQpmb3IgbGluZSBpbiBwcm9jLnN0ZG91dDoKICAgIGxpbmVfcyA9IGxpbmUuc3RyaXAoKQogICAgaWYgbm90IGxpbmVfcy5zdGFydHN3aXRoKCd7Jyk6CiAgICAgICAgc3lzLnN0ZG91dC53cml0ZShsaW5lKQogICAgICAgIHN5cy5zdGRvdXQuZmx1c2goKQogICAgICAgIGNvbnRpbnVlCiAgICB0cnk6CiAgICAgICAgb2JqID0ganNvbi5sb2FkcyhsaW5lX3MpCiAgICAgICAgdCA9IG9iai5nZXQoJ3R5cGUnLCAnJykKICAgICAgICBpZiB0ID09ICd0ZXN0U3RhcnQnOgogICAgICAgICAgICB0ZXN0c1tvYmpbJ3Rlc3QnXVsnaWQnXV0gPSBvYmpbJ3Rlc3QnXVsnbmFtZSddCiAgICAgICAgZWxpZiB0ID09ICd0ZXN0RG9uZScgYW5kIG5vdCBvYmouZ2V0KCdoaWRkZW4nLCBGYWxzZSk6CiAgICAgICAgICAgIG4gPSB0ZXN0cy5nZXQob2JqWyd0ZXN0SUQnXSwgJ3Vua25vd24nKQogICAgICAgICAgICByID0gb2JqLmdldCgncmVzdWx0JywgJycpCiAgICAgICAgICAgIGlmIHIgPT0gJ3N1Y2Nlc3MnOgogICAgICAgICAgICAgICAgcHJpbnQoJ1BBU1MgWzAuMDAxc10gJyArIG4sIGZsdXNoPVRydWUpCiAgICAgICAgICAgIGVsaWYgciBpbiAoJ2ZhaWx1cmUnLCAnZXJyb3InKToKICAgICAgICAgICAgICAgIHByaW50KCdGQUlMIFswLjAwMXNdICcgKyBuLCBmbHVzaD1UcnVlKQogICAgICAgICAgICAgICAgZmFpbGVkLmFkZChuKQogICAgICAgIGVsaWYgdCA9PSAnZXJyb3InOgogICAgICAgICAgICBzeXMuc3Rkb3V0LndyaXRlKG9iai5nZXQoJ2Vycm9yJywgJycpICsgY2hyKDEwKSkKICAgICAgICAgICAgc3lzLnN0ZG91dC5mbHVzaCgpCiAgICBleGNlcHQgRXhjZXB0aW9uOgogICAgICAgIHN5cy5zdGRvdXQud3JpdGUobGluZSkKICAgICAgICBzeXMuc3Rkb3V0LmZsdXNoKCkKcHJvYy53YWl0KCkKc3lzLmV4aXQocHJvYy5yZXR1cm5jb2RlKQo=" | base64 -d > /workspace/dart_test_runner.py

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# OFFLINE-CARGO-BAKE
RUN cd /workspace/repo && (cargo fetch 2>/dev/null; cargo test --no-run 2>/dev/null; cargo nextest run --no-run 2>/dev/null; true)
ENV CARGO_NET_OFFLINE=true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
