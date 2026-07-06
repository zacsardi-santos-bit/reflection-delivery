FROM golang:1.22

RUN apt-get update && apt-get install -y \
    python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/dolthub/dolt.git && \
    git fetch --depth 30 origin a516aa4ef736171c1796a4bcbcf26578072f8ffd && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN cd /workspace/repo/go && go build -o /usr/local/bin/dolt ./cmd/dolt/

RUN echo "IyEvYmluL2Jhc2gKaWYgZG9sdCBhZG1pbiB6c3RkID4gL2Rldi9udWxsIDI+JjE7IHRoZW4KICBlY2hvICd7IkFjdGlvbiI6InJ1biIsIlRlc3QiOiJ6c3RkX2FkbWluX3pzdGRfd29ya3MifScKICBlY2hvICd7IkFjdGlvbiI6InBhc3MiLCJUZXN0IjoienN0ZF9hZG1pbl96c3RkX3dvcmtzIiwiRWxhcHNlZCI6MC4wMDF9JwogIGVjaG8gJ3siQWN0aW9uIjoicGFzcyIsIkVsYXBzZWQiOjAuMDAxfScKZWxzZQogIGVjaG8gJ3siQWN0aW9uIjoicnVuIiwiVGVzdCI6InpzdGRfYWRtaW5fenN0ZF93b3JrcyJ9JwogIGVjaG8gJ3siQWN0aW9uIjoiZmFpbCIsIlRlc3QiOiJ6c3RkX2FkbWluX3pzdGRfd29ya3MiLCJFbGFwc2VkIjowLjAwMX0nCiAgZWNobyAneyJBY3Rpb24iOiJmYWlsIiwiRWxhcHNlZCI6MC4wMDF9JwogIGV4aXQgMQpmaQo=" | base64 -d > /usr/local/bin/test_dolt_zstd && chmod +x /usr/local/bin/test_dolt_zstd

RUN dolt config --global --add user.name "Bats Tests" && \
    dolt config --global --add user.email "bats@example.com"

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# --- offline-harbor go-bake v1 ---
RUN set +e; cd /workspace/repo && for gm in $(find . -name go.mod -not -path '*/vendor/*' -not -path '*/testdata/*' -not -path '*/node_modules/*' 2>/dev/null); do d=$(dirname "$gm"); ( cd "$d" && GOWORK=off GOFLAGS=-mod=mod go mod download all 2>/dev/null ); done; true
RUN set +e; cd /workspace/repo/go 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./cmd/dolt/ 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
github.com/dolthub/gozstd@v0.0.0-20240423170813-23a2903bca63
__GOBAKE__
RUN set +e; cd /workspace/repo && d=$(dirname $(find . -name go.mod -not -path '*/vendor/*' 2>/dev/null | head -1)); [ -z "$d" ] && d=.; cd "$d"; while read -r c; do [ -n "$c" ] && GOWORK=off GOFLAGS=-mod=mod go mod download "$c" 2>/dev/null; done < /tmp/_gobake_coords.txt; true
ENV GOWORK=off
ENV GOFLAGS=-mod=mod
ENV GOPROXY=off
ENV GOSUMDB=off

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
