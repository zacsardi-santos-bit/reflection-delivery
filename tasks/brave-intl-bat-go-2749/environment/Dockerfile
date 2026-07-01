FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/brave-intl/bat-go.git && \
    git fetch --depth 30 origin a874020835d6c2091157810885a5e7579b28978f && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/libs

RUN go mod download || true

WORKDIR /workspace/repo/services

RUN go mod download || true


# --- offline dep-baking: warm Go module cache at build (network available here) ---
RUN cd /workspace/repo && \
    for d in $(find . -name go.mod -not -path '*/vendor/*' -exec dirname {} \; | sort -u); do \
      ( cd "$d" && echo "warming $d" && \
        (go mod download all 2>/dev/null || go mod download 2>/dev/null || true) && \
        (go test -count=1 -run '^$' ./... 2>/dev/null || true) ) ; \
    done; true
# warm exact dep versions the fix/test introduce (module coordinates only, not solution code)
RUN cd /workspace/repo && for m in \
    github.com/bsm/ginkgo/v2@v2.12.0 \
    github.com/bsm/ginkgo/v2@v2.7.0 \
    github.com/bsm/gomega@v1.26.0 \
    github.com/bsm/gomega@v1.27.10 \
    github.com/dgryski/go-rendezvous@v0.0.0-20200823014737-9f7001d12a5f \
    github.com/fsnotify/fsnotify@v1.4.7 \
    github.com/fsnotify/fsnotify@v1.4.9 \
    github.com/go-redis/redis/v8@v8.4.2 \
    github.com/go-redis/redis@v6.15.8+incompatible \
    github.com/gomodule/redigo@v1.8.9 \
    github.com/google/go-cmp@v0.5.3 \
    github.com/hpcloud/tail@v1.0.0 \
    github.com/nxadm/tail@v1.4.4 \
    github.com/onsi/ginkgo@v1.12.1 \
    github.com/onsi/ginkgo@v1.14.2 \
    github.com/onsi/ginkgo@v1.6.0 \
    github.com/onsi/gomega@v1.10.1 \
    github.com/onsi/gomega@v1.10.3 \
    github.com/onsi/gomega@v1.7.1 \
    github.com/redis/go-redis/v9@v9.0.5 \
    github.com/redis/go-redis/v9@v9.7.0 \
    github.com/stretchr/testify@v1.6.1 \
    github.com/throttled/throttled/v2@v2.12.0 \
    github.com/yuin/goldmark@v1.4.13 \
    go.opentelemetry.io/otel@v0.14.0 \
    golang.org/x/crypto@v0.0.0-20210921155107-089bfa567519 \
    golang.org/x/mod@v0.6.0-dev.0.20220419223038-86c51ed26bb4 \
    golang.org/x/net@v0.0.0-20180906233101-161cd47e91fd \
    golang.org/x/net@v0.0.0-20200520004742-59133d7f0dd7 \
    golang.org/x/net@v0.0.0-20201006153459-a7d1128ccaa0 \
    golang.org/x/net@v0.0.0-20220722155237-a158d28d115b \
    golang.org/x/sync@v0.0.0-20220722155255-886fb9371eb4 \
    golang.org/x/sys@v0.0.0-20180909124046-d0be0721c37e \
    golang.org/x/sys@v0.0.0-20190904154756-749cb33beabd \
    golang.org/x/sys@v0.0.0-20191005200804-aed5e4c7ecf9 \
    golang.org/x/sys@v0.0.0-20191120155948-bd437916bb0e \
    golang.org/x/sys@v0.0.0-20200519105757-fe76b779f299 \
    golang.org/x/sys@v0.0.0-20220520151302-bc2c85ada10a \
    golang.org/x/sys@v0.0.0-20220722155257-8c9f86f7a55f \
    golang.org/x/text@v0.3.8 \
    golang.org/x/tools@v0.1.12 \
    gopkg.in/fsnotify.v1@v1.4.7 \
    gopkg.in/tomb.v1@v1.0.0-20141024135613-dd632973f1e7 \
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
