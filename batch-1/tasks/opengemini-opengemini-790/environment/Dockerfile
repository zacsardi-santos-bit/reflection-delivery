FROM golang:1.22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/openGemini/openGemini.git && \
    git fetch --depth 30 origin 6be5081851f006d99e203ce02fed760c5faffaf1 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN find . -name "*.conf" | xargs sed -i 's/\r$//' && \
    git config user.email "build@example.com" && \
    git config user.name "Build" && \
    git add -A && \
    git commit -m "normalize line endings" || true

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
    cloud.google.com/go/compute/metadata@v0.3.0 \
    github.com/alecthomas/units@v0.0.0-20211218093645-b94a6e3cc137 \
    github.com/cespare/xxhash/v2@v2.3.0 \
    github.com/cncf/xds/go@v0.0.0-20240423153145-555b57ec207b \
    github.com/go-kit/log@v0.2.1 \
    github.com/klauspost/compress@v1.17.11 \
    github.com/kylelemons/godebug@v1.1.0 \
    github.com/munnerz/goautoneg@v0.0.0-20191010083416-a7dc8b61c822 \
    github.com/openGemini/opengemini-client-go@v0.8.1 \
    github.com/prometheus/client_golang@v1.20.5 \
    github.com/prometheus/client_model@v0.6.1 \
    github.com/prometheus/common@v0.55.0 \
    github.com/prometheus/procfs@v0.15.1 \
    github.com/stretchr/testify@v1.10.0 \
    golang.org/x/net@v0.26.0 \
    golang.org/x/oauth2@v0.21.0 \
    google.golang.org/genproto/googleapis/api@v0.0.0-20240528184218-531527333157 \
    google.golang.org/genproto/googleapis/rpc@v0.0.0-20240528184218-531527333157 \
    google.golang.org/grpc@v1.65.1 \
    google.golang.org/protobuf@v1.35.2 \
    ; do go mod download "$m" 2>/dev/null || true; done; true
ENV GOFLAGS=-mod=mod
ENV GOPROXY=off
ENV GOSUMDB=off

WORKDIR /workspace

# >>> final-git-hygiene (compliance) >>>
RUN cd /workspace/repo 2>/dev/null && ( \
    B=6be5081851f006d99e203ce02fed760c5faffaf1; H=$(git rev-parse HEAD 2>/dev/null); \
    if [ "$H" != "$B" ]; then git reset --soft "$B" 2>/dev/null || true; fi; \
    git remote remove origin 2>/dev/null || true; \
    git tag -l 2>/dev/null | xargs -r git tag -d 2>/dev/null || true; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    git gc --prune=now 2>/dev/null || true; \
    rm -rf .git/logs ) ; true
# <<< final-git-hygiene (compliance) <<<

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
