FROM mcr.microsoft.com/dotnet/sdk:8.0

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch \
    pulseaudio libpulse0 libasound2-plugins \
    python3 \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /etc/pulse && printf 'load-module module-null-sink\nload-module module-native-protocol-unix auth-anonymous=1 socket=/tmp/pulse-socket\n' > /etc/pulse/default.pa

RUN printf 'pcm.pulse { type pulse ; server "unix:/tmp/pulse-socket" }\npcm.!default { type pulse ; server "unix:/tmp/pulse-socket" }\nctl.!default { type pulse ; server "unix:/tmp/pulse-socket" }\n' > /etc/asound.conf

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ppy/osu.git && \
    git fetch --depth 30 origin 75d1fab6d064754574a0360b44061923241c3e43 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN dotnet restore osu.Game.Tests/osu.Game.Tests.csproj

WORKDIR /workspace

RUN echo "IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgc3VicHJvY2Vzcywgc3lzLCBvcywgcmUKdHJ5OgogICAgaW1wb3J0IHhtbC5ldHJlZS5FbGVtZW50VHJlZSBhcyBFVApleGNlcHQgSW1wb3J0RXJyb3I6CiAgICBFVCA9IE5vbmUKCmp1bml0eG1sX3BhdGggPSBOb25lCmRvdG5ldF9hcmdzID0gW10KZm9yIGFyZyBpbiBzeXMuYXJndlsxOl06CiAgICBpZiBhcmcuc3RhcnRzd2l0aCgnLS1qdW5pdHhtbD0nKToKICAgICAgICBqdW5pdHhtbF9wYXRoID0gYXJnW2xlbignLS1qdW5pdHhtbD0nKTpdCiAgICBlbHNlOgogICAgICAgIGRvdG5ldF9hcmdzLmFwcGVuZChhcmcpCgpjbWQgPSBbJ2RvdG5ldCcsICd0ZXN0J10gKyBkb3RuZXRfYXJncwpwcm9jID0gc3VicHJvY2Vzcy5ydW4oY21kLCBjYXB0dXJlX291dHB1dD1UcnVlLCB0ZXh0PVRydWUpCnByaW50KHByb2Muc3Rkb3V0KQppZiBwcm9jLnN0ZGVycjoKICAgIHByaW50KHByb2Muc3RkZXJyLCBmaWxlPXN5cy5zdGRlcnIpCgp0ZXN0cyA9IFtdCmZvciBsaW5lIGluIHByb2Muc3Rkb3V0LnNwbGl0KCdcbicpOgogICAgbSA9IHJlLm1hdGNoKHInXiAgKFBhc3NlZHxGYWlsZWQpIChcUyspJywgbGluZSkKICAgIGlmIG06CiAgICAgICAgdGVzdHMuYXBwZW5kKChtLmdyb3VwKDEpLCBtLmdyb3VwKDIpKSkKICAgICAgICBzdGF0dXMgPSAnUEFTU0VEJyBpZiBtLmdyb3VwKDEpID09ICdQYXNzZWQnIGVsc2UgJ0ZBSUxFRCcKICAgICAgICBwcmludChzdGF0dXMgKyAnICcgKyBtLmdyb3VwKDIpKQoKaWYganVuaXR4bWxfcGF0aCBhbmQgRVQgaXMgbm90IE5vbmU6CiAgICBvcy5tYWtlZGlycyhvcy5wYXRoLmRpcm5hbWUoanVuaXR4bWxfcGF0aCksIGV4aXN0X29rPVRydWUpCiAgICByb290ID0gRVQuRWxlbWVudCgndGVzdHN1aXRlJykKICAgIHJvb3Quc2V0KCduYW1lJywgJ05Vbml0JykKICAgIHJvb3Quc2V0KCd0ZXN0cycsIHN0cihsZW4odGVzdHMpKSkKICAgIHJvb3Quc2V0KCdmYWlsdXJlcycsIHN0cihzdW0oMSBmb3IgdCBpbiB0ZXN0cyBpZiB0WzBdID09ICdGYWlsZWQnKSkpCiAgICByb290LnNldCgnZXJyb3JzJywgJzAnKQogICAgcm9vdC5zZXQoJ3RpbWUnLCAnMCcpCiAgICBmb3Igc3RhdHVzLCBuYW1lIGluIHRlc3RzOgogICAgICAgIHRjID0gRVQuU3ViRWxlbWVudChyb290LCAndGVzdGNhc2UnKQogICAgICAgIHRjLnNldCgnbmFtZScsIG5hbWUpCiAgICAgICAgdGMuc2V0KCdjbGFzc25hbWUnLCAnVGVzdFNjZW5lVXNlckRpbUJhY2tncm91bmRzJykKICAgICAgICB0Yy5zZXQoJ3RpbWUnLCAnMCcpCiAgICAgICAgaWYgc3RhdHVzID09ICdGYWlsZWQnOgogICAgICAgICAgICBmYWlsdXJlID0gRVQuU3ViRWxlbWVudCh0YywgJ2ZhaWx1cmUnKQogICAgICAgICAgICBmYWlsdXJlLnNldCgnbWVzc2FnZScsICdUZXN0IGZhaWxlZCcpCiAgICAgICAgICAgIGZhaWx1cmUudGV4dCA9ICdUZXN0IGZhaWxlZCcKICAgIEVULkVsZW1lbnRUcmVlKHJvb3QpLndyaXRlKGp1bml0eG1sX3BhdGgsIGVuY29kaW5nPSd1bmljb2RlJywgeG1sX2RlY2xhcmF0aW9uPVRydWUpCgpzeXMuZXhpdChwcm9jLnJldHVybmNvZGUpCg==" | base64 -d > /workspace/run_nunit.py && chmod +x /workspace/run_nunit.py

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
