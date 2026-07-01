FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    openjdk-17-jdk \
    clang \
    git \
    curl \
    gnupg \
    python3 \
    zip \
    unzip \
    patch \
    build-essential \
    ca-certificates \
    apt-transport-https \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://bazel.build/bazel-release.pub.gpg | gpg --dearmor > /usr/share/keyrings/bazel-archive-keyring.gpg \
    && echo "deb [arch=amd64 signed-by=/usr/share/keyrings/bazel-archive-keyring.gpg] https://storage.googleapis.com/bazel-apt stable jdk1.8" > /etc/apt/sources.list.d/bazel.list \
    && apt-get update && apt-get install -y bazel-7.4.1 \
    && ln -sf /usr/bin/bazel-7.4.1 /usr/bin/bazel \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/world-federation-of-advertisers/cross-media-measurement.git && \
    git fetch --depth 30 origin ab9be6c7f728d097d1befe06e855e4b9cce2b482 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN echo 'aW1wb3J0IHN5cywgZ2xvYiwgeG1sLmV0cmVlLkVsZW1lbnRUcmVlIGFzIEVULCBqc29uCgpleGl0X2NvZGUgPSAwCmFyZ3MgPSBzeXMuYXJndlsxOl0KaSA9IDAKd2hpbGUgaSA8IGxlbihhcmdzKToKICAgIGlmIGFyZ3NbaV0gPT0gIi0tZXhpdCIgYW5kIGkgKyAxIDwgbGVuKGFyZ3MpOgogICAgICAgIHRyeToKICAgICAgICAgICAgZXhpdF9jb2RlID0gaW50KGFyZ3NbaSArIDFdKQogICAgICAgIGV4Y2VwdCBFeGNlcHRpb246CiAgICAgICAgICAgIHBhc3MKICAgICAgICBpICs9IDIKICAgIGVsc2U6CiAgICAgICAgaSArPSAxCgpmb3IgeG1sX2ZpbGUgaW4gZ2xvYi5nbG9iKCJiYXplbC10ZXN0bG9ncy8qKi8qLnhtbCIsIHJlY3Vyc2l2ZT1UcnVlKToKICAgIHRyeToKICAgICAgICB0cmVlID0gRVQucGFyc2UoeG1sX2ZpbGUpCiAgICAgICAgcm9vdCA9IHRyZWUuZ2V0cm9vdCgpCiAgICAgICAgc3VpdGVzID0gW3Jvb3RdIGlmIHJvb3QudGFnID09ICJ0ZXN0c3VpdGUiIGVsc2Ugcm9vdC5maW5kYWxsKCJ0ZXN0c3VpdGUiKQogICAgICAgIGZvciBzdWl0ZSBpbiBzdWl0ZXM6CiAgICAgICAgICAgIHBrZyA9IHN1aXRlLmdldCgibmFtZSIsICIiKQogICAgICAgICAgICBmb3IgdGMgaW4gc3VpdGUuZmluZGFsbCgidGVzdGNhc2UiKToKICAgICAgICAgICAgICAgIG5hbWUgPSB0Yy5nZXQoIm5hbWUiLCAiIikucmVwbGFjZSgiICIsICJfIikKICAgICAgICAgICAgICAgIHRpbWVfdmFsID0gdGMuZ2V0KCJ0aW1lIiwgIjAuMDAiKQogICAgICAgICAgICAgICAgdHJ5OgogICAgICAgICAgICAgICAgICAgIHQgPSBmbG9hdCh0aW1lX3ZhbCkKICAgICAgICAgICAgICAgIGV4Y2VwdCBFeGNlcHRpb246CiAgICAgICAgICAgICAgICAgICAgdCA9IDAuMAogICAgICAgICAgICAgICAgaWYgdGMuZmluZCgiZmFpbHVyZSIpIGlzIG5vdCBOb25lIG9yIHRjLmZpbmQoImVycm9yIikgaXMgbm90IE5vbmU6CiAgICAgICAgICAgICAgICAgICAgYWN0aW9uID0gImZhaWwiCiAgICAgICAgICAgICAgICBlbHNlOgogICAgICAgICAgICAgICAgICAgIGFjdGlvbiA9ICJwYXNzIgogICAgICAgICAgICAgICAgcHJpbnQoanNvbi5kdW1wcyh7IkFjdGlvbiI6IGFjdGlvbiwgIlBhY2thZ2UiOiBwa2csICJUZXN0IjogbmFtZSwgIkVsYXBzZWQiOiB0fSkpCiAgICBleGNlcHQgRXhjZXB0aW9uOgogICAgICAgIHBhc3MKCnN5cy5leGl0KGV4aXRfY29kZSkK' | base64 -d > /usr/local/bin/convert-junit.py

WORKDIR /workspace/repo

RUN bazel build \
    //src/test/kotlin/org/wfanet/measurement/measurementconsumer/stats:covariances_test \
    //src/test/kotlin/org/wfanet/measurement/reporting/service/api/v2alpha:MetricSpecDefaultsTest \
    //src/test/kotlin/org/wfanet/measurement/kingdom/service/api/v2alpha:MeasurementsServiceTest

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
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
