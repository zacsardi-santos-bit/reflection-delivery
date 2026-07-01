FROM golang:1.22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/fission/fission.git && \
    git fetch --depth 30 origin f7e9e71ee12ee7e56d73159e00d6de04afecd23e && \
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

RUN go build ./... || true


# --- offline dep-baking: warm Go module cache at build (network available here) ---
RUN cd /workspace/repo && \
    for d in $(find . -name go.mod -not -path '*/vendor/*' -exec dirname {} \; | sort -u); do \
      ( cd "$d" && echo "warming $d" && \
        (go mod download all 2>/dev/null || go mod download 2>/dev/null || true) && \
        (go test -count=1 -run '^$' ./... 2>/dev/null || true) ) ; \
    done; true
# warm exact dep versions the fix/test introduce (module coordinates only, not solution code)
RUN cd /workspace/repo && for m in \
    github.com/IBM/sarama@v1.43.2 \
    github.com/eapache/go-resiliency@v1.6.0 \
    github.com/eapache/go-xerial-snappy@v0.0.0-20230731223053-c322873962e3 \
    github.com/eapache/queue@v1.1.0 \
    github.com/fortytw2/leaktest@v1.3.0 \
    github.com/gorilla/securecookie@v1.1.1 \
    github.com/gorilla/sessions@v1.2.1 \
    github.com/hashicorp/go-uuid@v1.0.2 \
    github.com/hashicorp/go-uuid@v1.0.3 \
    github.com/jcmturner/aescts/v2@v2.0.0 \
    github.com/jcmturner/dnsutils/v2@v2.0.0 \
    github.com/jcmturner/gofork@v1.7.6 \
    github.com/jcmturner/goidentity/v6@v6.0.1 \
    github.com/jcmturner/gokrb5/v8@v8.4.4 \
    github.com/jcmturner/rpc/v2@v2.0.3 \
    github.com/klauspost/compress@v1.17.8 \
    github.com/pierrec/lz4/v4@v4.1.21 \
    github.com/rcrowley/go-metrics@v0.0.0-20201227073835-cf1acfcdf475 \
    golang.org/x/crypto@v0.6.0 \
    golang.org/x/net@v0.0.0-20200114155413-6afb5195e5aa \
    golang.org/x/net@v0.7.0 \
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
