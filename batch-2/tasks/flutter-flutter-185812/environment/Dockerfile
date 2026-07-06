FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential unzip python3 \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://storage.googleapis.com/dart-archive/channels/stable/release/latest/sdk/dartsdk-linux-x64-release.zip -o /tmp/dart-sdk.zip \
    && unzip -q /tmp/dart-sdk.zip -d /usr/lib \
    && rm /tmp/dart-sdk.zip

ENV PATH="/usr/lib/dart-sdk/bin:${PATH}"

RUN echo "aW1wb3J0IHN5cywganNvbgppZHMgPSB7fQpmb3IgbGluZSBpbiBzeXMuc3RkaW46CiAgICBsaW5lID0gbGluZS5zdHJpcCgpCiAgICBpZiBub3QgbGluZToKICAgICAgICBjb250aW51ZQogICAgdHJ5OgogICAgICAgIGUgPSBqc29uLmxvYWRzKGxpbmUpCiAgICAgICAgdCA9IGUuZ2V0KCJ0eXBlIikKICAgICAgICBpZiB0ID09ICJ0ZXN0U3RhcnQiOgogICAgICAgICAgICBpZHNbZVsidGVzdCJdWyJpZCJdXSA9IGVbInRlc3QiXVsibmFtZSJdCiAgICAgICAgZWxpZiB0ID09ICJ0ZXN0RG9uZSI6CiAgICAgICAgICAgIGlmIGUuZ2V0KCJoaWRkZW4iLCBGYWxzZSk6CiAgICAgICAgICAgICAgICBjb250aW51ZQogICAgICAgICAgICB0aWQgPSBlLmdldCgidGVzdElEIikKICAgICAgICAgICAgaWYgdGlkIGluIGlkczoKICAgICAgICAgICAgICAgIHNrID0gZS5nZXQoInNraXBwZWQiLCBGYWxzZSkKICAgICAgICAgICAgICAgIHIgPSBlLmdldCgicmVzdWx0IiwgInN1Y2Nlc3MiKQogICAgICAgICAgICAgICAgYSA9ICJza2lwIiBpZiBzayBlbHNlICgicGFzcyIgaWYgciA9PSAic3VjY2VzcyIgZWxzZSAiZmFpbCIpCiAgICAgICAgICAgICAgICBwcmludChqc29uLmR1bXBzKHsiQWN0aW9uIjogYSwgIlRlc3QiOiBpZHNbdGlkXSwgIlBhY2thZ2UiOiAieCJ9KSwgZmx1c2g9VHJ1ZSkKICAgIGV4Y2VwdCBFeGNlcHRpb246CiAgICAgICAgcGFzcwo=" | base64 -d > /usr/local/bin/dart_to_go.py

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flutter/flutter.git && \
    git fetch --depth 30 origin 79557800f99214fa78d7a77d1e273d39c9077933 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cd /workspace/repo/packages/flutter_tools && dart pub get

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
# --- offline toolchain fix: ensure the language toolchain (go) is installed on the login PATH ---
RUN command -v go >/dev/null 2>&1 \
    || (apt-get update && apt-get install -y --no-install-recommends golang-go && rm -rf /var/lib/apt/lists/*) \
    || (apk add --no-cache go) || (dnf install -y golang) || (yum install -y golang) || true

RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
