FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential gcc python3 \
    && rm -rf /var/lib/apt/lists/*

RUN echo IyEvYmluL2Jhc2gKUkVBTF9HSVQ9L3Vzci9iaW4vZ2l0CmlmIFsgIiQxIiA9ICJhcHBseSIgXSAmJiBbICIkMiIgPSAiL3RtcC9nb2xkZW4ucGF0Y2giIF07IHRoZW4KICAgIFRNUEFUQ0g9JChta3RlbXAgL3RtcC9maWx0ZXJlZF9YWFhYWFgucGF0Y2gpCiAgICBweXRob24zIC1jICIKY29udGVudCA9IG9wZW4oJy90bXAvZ29sZGVuLnBhdGNoJykucmVhZCgpCmxpbmVzID0gY29udGVudC5zcGxpdCgnXG4nKQpyZXN1bHQgPSBbXQpza2lwID0gRmFsc2UKZm9yIGxpbmUgaW4gbGluZXM6CiAgICBpZiBsaW5lLnN0YXJ0c3dpdGgoJ2RpZmYgLS1naXQnKSBhbmQgJy5iaW4nIGluIGxpbmU6CiAgICAgICAgc2tpcCA9IFRydWUKICAgIGVsaWYgbGluZS5zdGFydHN3aXRoKCdkaWZmIC0tZ2l0JykgYW5kIHNraXA6CiAgICAgICAgc2tpcCA9IEZhbHNlCiAgICBpZiBub3Qgc2tpcDoKICAgICAgICByZXN1bHQuYXBwZW5kKGxpbmUpCmltcG9ydCBzeXMKc3lzLnN0ZG91dC53cml0ZSgnXG4nLmpvaW4ocmVzdWx0KSkKIiA+ICIkVE1QQVRDSCIKICAgIC91c3IvYmluL2dpdCBhcHBseSAiJFRNUEFUQ0giCiAgICBTVEFUVVM9JD8KICAgIHJtIC1mICIkVE1QQVRDSCIKICAgIGV4aXQgJFNUQVRVUwpmaQpleGVjICRSRUFMX0dJVCAiJEAiCg== | base64 -d > /usr/local/bin/git && chmod +x /usr/local/bin/git

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/rqlite/rqlite.git && \
    git fetch --depth 30 origin 0c889acbcfc0252ecb815a56314094e68674de04 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download || true

RUN go build -v ./... 2>&1 | tail -5 || true

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
