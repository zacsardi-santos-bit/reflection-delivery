FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential unzip python3 \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://storage.googleapis.com/dart-archive/channels/stable/release/2.12.4/sdk/dartsdk-linux-x64-release.zip -o /tmp/dart.zip && \
    unzip /tmp/dart.zip -d /usr/local && \
    rm /tmp/dart.zip

ENV PATH="/usr/local/dart-sdk/bin:${PATH}"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 94a30ddcbb4f28dc2143509b6f8eb9ee99779b69 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/packages/flutter_tools

RUN dart pub get 2>&1 | tail -50

RUN echo "IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgc3VicHJvY2VzcywganNvbiwgcmUsIHN5cywgb3MKCmRlZiBzYW5pdGl6ZShuYW1lKToKICAgIHJldHVybiByZS5zdWIocidbXmEtekEtWjAtOV9dJywgJ18nLCBuYW1lLnN0cmlwKCkpLnN0cmlwKCdfJykKCmRlZiBnZXRfbmFtZXMocGF0aCk6CiAgICBuYW1lcyA9IFtdCiAgICB0cnk6CiAgICAgICAgYyA9IG9wZW4ocGF0aCkucmVhZCgpCiAgICAgICAgbmFtZXMgKz0gcmUuZmluZGFsbChyInRlc3RXaXRob3V0Q29udGV4dFxzKlwoXHMqJyhbXiddKyknIiwgYykKICAgICAgICBuYW1lcyArPSByZS5maW5kYWxsKHIndGVzdFdpdGhvdXRDb250ZXh0XHMqXChccyoiKFteIl0rKSInLCBjKQogICAgICAgIG5hbWVzICs9IHJlLmZpbmRhbGwociIoPzwhXHcpdGVzdFxzKlwoXHMqJyhbXiddKyknIiwgYykKICAgICAgICBuYW1lcyArPSByZS5maW5kYWxsKHInKD88IVx3KXRlc3RccypcKFxzKiIoW14iXSspIicsIGMpCiAgICBleGNlcHQ6CiAgICAgICAgcGFzcwogICAgcmV0dXJuIG5hbWVzCgpkZWYgd3JpdGVfanVuaXQocGF0aCwgdGVzdF9maWxlLCByZXN1bHRzKToKICAgIGxpbmVzID0gWyc8P3htbCB2ZXJzaW9uPSIxLjAiIGVuY29kaW5nPSJVVEYtOCI/PiddCiAgICB0b3RhbCA9IGxlbihyZXN1bHRzKQogICAgZmFpbHVyZXMgPSBzdW0oMSBmb3IgcyBpbiByZXN1bHRzLnZhbHVlcygpIGlmIHMgPT0gJ0ZBSUxFRCcpCiAgICBsaW5lcy5hcHBlbmQoZic8dGVzdHN1aXRlcz48dGVzdHN1aXRlIG5hbWU9ImRhcnRfdGVzdHMiIHRlc3RzPSJ7dG90YWx9IiBmYWlsdXJlcz0ie2ZhaWx1cmVzfSIgZXJyb3JzPSIwIiB0aW1lPSIxLjAiPicpCiAgICBmb3IgbmFtZSwgc3RhdHVzIGluIHJlc3VsdHMuaXRlbXMoKToKICAgICAgICBpZiBzdGF0dXMgPT0gJ1BBU1NFRCc6CiAgICAgICAgICAgIGxpbmVzLmFwcGVuZChmJyAgPHRlc3RjYXNlIGNsYXNzbmFtZT0ie3Rlc3RfZmlsZX0iIG5hbWU9IntuYW1lfSIgdGltZT0iMC4xIi8+JykKICAgICAgICBlbHNlOgogICAgICAgICAgICBsaW5lcy5hcHBlbmQoZicgIDx0ZXN0Y2FzZSBjbGFzc25hbWU9Int0ZXN0X2ZpbGV9IiBuYW1lPSJ7bmFtZX0iIHRpbWU9IjAuMSI+PGZhaWx1cmUgbWVzc2FnZT0iVGVzdCBmYWlsZWQiLz48L3Rlc3RjYXNlPicpCiAgICBsaW5lcy5hcHBlbmQoJzwvdGVzdHN1aXRlPjwvdGVzdHN1aXRlcz4nKQogICAgb3MubWFrZWRpcnMob3MucGF0aC5kaXJuYW1lKHBhdGgpLCBleGlzdF9vaz1UcnVlKQogICAgd2l0aCBvcGVuKHBhdGgsICd3JykgYXMgZjoKICAgICAgICBmLndyaXRlKCdcbicuam9pbihsaW5lcykpCgphbGxfYXJncyA9IHN5cy5hcmd2WzE6XQpqdW5pdF9wYXRoID0gTm9uZQpkYXJ0X2FyZ3MgPSBbXQpmb3IgYSBpbiBhbGxfYXJnczoKICAgIGlmIGEuc3RhcnRzd2l0aCgnLS1qdW5pdHhtbD0nKToKICAgICAgICBqdW5pdF9wYXRoID0gYVtsZW4oJy0tanVuaXR4bWw9Jyk6XQogICAgZWxpZiBhLnN0YXJ0c3dpdGgoJy0tanVuaXR4bWwnKToKICAgICAgICBwYXNzCiAgICBlbHNlOgogICAgICAgIGRhcnRfYXJncy5hcHBlbmQoYSkKCnRlc3RfZmlsZSA9IG5leHQoKGEgZm9yIGEgaW4gZGFydF9hcmdzIGlmIG5vdCBhLnN0YXJ0c3dpdGgoJy0nKSksIE5vbmUpCgpwcm9jID0gc3VicHJvY2Vzcy5ydW4oWydkYXJ0JywgJ3Rlc3QnLCAnLS1yZXBvcnRlcicsICdqc29uJ10gKyBkYXJ0X2FyZ3MsCiAgICBjYXB0dXJlX291dHB1dD1UcnVlLCB0ZXh0PVRydWUpCnN5cy5zdGRlcnIud3JpdGUocHJvYy5zdGRlcnIpCgp0ZXN0cywgcmVzdWx0cyA9IHt9LCB7fQpmb3IgbGluZSBpbiBwcm9jLnN0ZG91dC5zcGxpdCgnXG4nKToKICAgIGxpbmUgPSBsaW5lLnN0cmlwKCkKICAgIGlmIG5vdCBsaW5lOgogICAgICAgIGNvbnRpbnVlCiAgICB0cnk6CiAgICAgICAgZXYgPSBqc29uLmxvYWRzKGxpbmUpCiAgICBleGNlcHQ6CiAgICAgICAgY29udGludWUKICAgIGV0ID0gZXYuZ2V0KCd0eXBlJywgJycpCiAgICBpZiBldCA9PSAndGVzdFN0YXJ0JzoKICAgICAgICB0ID0gZXYuZ2V0KCd0ZXN0Jywge30pCiAgICAgICAgdGVzdHNbdC5nZXQoJ2lkJyldID0gdC5nZXQoJ25hbWUnLCAnJykKICAgIGVsaWYgZXQgPT0gJ3Rlc3REb25lJzoKICAgICAgICB0aWQgPSBldi5nZXQoJ3Rlc3RJRCcpCiAgICAgICAgaGlkZGVuID0gZXYuZ2V0KCdoaWRkZW4nLCBGYWxzZSkKICAgICAgICByZXMgPSBldi5nZXQoJ3Jlc3VsdCcsICdlcnJvcicpCiAgICAgICAgaWYgbm90IGhpZGRlbiBhbmQgdGlkIGluIHRlc3RzOgogICAgICAgICAgICBuYW1lID0gdGVzdHNbdGlkXQogICAgICAgICAgICBpZiBuYW1lIGFuZCBub3QgbmFtZS5zdGFydHN3aXRoKCdsb2FkaW5nJyk6CiAgICAgICAgICAgICAgICByZXN1bHRzW3Nhbml0aXplKG5hbWUpXSA9ICdQQVNTRUQnIGlmIHJlcyA9PSAnc3VjY2VzcycgZWxzZSAnRkFJTEVEJwoKaWYgcHJvYy5yZXR1cm5jb2RlICE9IDAgYW5kIG5vdCByZXN1bHRzIGFuZCB0ZXN0X2ZpbGU6CiAgICBmb3IgbiBpbiBnZXRfbmFtZXModGVzdF9maWxlKToKICAgICAgICByZXN1bHRzW3Nhbml0aXplKG4pXSA9ICdGQUlMRUQnCiAgICBpZiBub3QgcmVzdWx0czoKICAgICAgICByZXN1bHRzWyd0ZXN0X3N1aXRlX2Vycm9yJ10gPSAnRkFJTEVEJwoKcGZ4ID0gKHRlc3RfZmlsZSArICc6OicpIGlmIHRlc3RfZmlsZSBlbHNlICcnCmZvciBuYW1lLCBzdGF0dXMgaW4gcmVzdWx0cy5pdGVtcygpOgogICAgcHJpbnQoZid7cGZ4fXtuYW1lfSB7c3RhdHVzfScpCgppZiBqdW5pdF9wYXRoIGFuZCByZXN1bHRzOgogICAgd3JpdGVfanVuaXQoanVuaXRfcGF0aCwgdGVzdF9maWxlIG9yICdkYXJ0X3Rlc3QnLCByZXN1bHRzKQoKaGFzX2ZhaWx1cmVzID0gYW55KHMgPT0gJ0ZBSUxFRCcgZm9yIHMgaW4gcmVzdWx0cy52YWx1ZXMoKSkKcmMgPSAxIGlmIChoYXNfZmFpbHVyZXMgb3IgKHByb2MucmV0dXJuY29kZSAhPSAwIGFuZCBub3QgcmVzdWx0cykpIGVsc2UgMApzeXMuZXhpdChyYykK" | base64 -d > /usr/local/bin/dart_test_runner.py && chmod +x /usr/local/bin/dart_test_runner.py

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
