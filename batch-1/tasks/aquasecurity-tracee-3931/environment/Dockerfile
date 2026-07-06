FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libelf-dev zlib1g-dev linux-libc-dev make \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/aquasecurity/tracee.git && \
    git fetch --depth 30 origin 04231fc0a7b2d2b72a40f61ac577ecdb29c8d93e && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN cd /workspace/repo && git submodule update --init 3rdparty/libbpf

RUN cd /workspace/repo && \
    mkdir -p dist/libbpf/obj && \
    CC=gcc CFLAGS="-fPIC" make -C ./3rdparty/libbpf/src \
        BUILD_STATIC_ONLY=1 \
        DESTDIR=/workspace/repo/dist/libbpf \
        OBJDIR=/workspace/repo/dist/libbpf/obj \
        INCLUDEDIR= LIBDIR= UAPIDIR= prefix= libdir= \
        install install_uapi_headers 2>&1 | tail -10

ENV CGO_CFLAGS="-I/workspace/repo/dist/libbpf"
ENV CGO_LDFLAGS="-lelf -lz /workspace/repo/dist/libbpf/libbpf.a"

WORKDIR /workspace/repo

RUN go mod download 2>&1 | tail -5 || true


# --- offline dep-baking: warm Go module cache at build (network available here) ---
RUN cd /workspace/repo && \
    for d in $(find . -name go.mod -not -path '*/vendor/*' -exec dirname {} \; | sort -u); do \
      ( cd "$d" && echo "warming $d" && \
        (go mod download all 2>/dev/null || go mod download 2>/dev/null || true) && \
        (go test -count=1 -run '^$' ./... 2>/dev/null || true) ) ; \
    done; true
# warm exact dep versions the fix/test introduce (module coordinates only, not solution code)
RUN cd /workspace/repo && for m in \
    google.golang.org/protobuf@v1.31.0 \
    ; do go mod download "$m" 2>/dev/null || true; done; true
ENV GOFLAGS=-mod=mod
ENV GOPROXY=off
ENV GOSUMDB=off

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
