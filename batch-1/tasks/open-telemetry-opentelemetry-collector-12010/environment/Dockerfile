FROM golang:1.22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/open-telemetry/opentelemetry-collector.git && \
    git fetch --depth 30 origin af09df1123ca5ff1b5a3787b447f8267e801d7a6 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/exporter

RUN go mod download || true

WORKDIR /workspace/repo/cmd/builder

RUN go mod download || true

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
RUN set +e; cd /workspace/repo/exporter 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./internal/queue ./internal/builder 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
github.com/davecgh/go-spew@v1.1.1
github.com/go-logr/logr@v1.4.2
github.com/go-logr/stdr@v1.2.2
github.com/gogo/protobuf@v1.3.2
github.com/golang/protobuf@v1.5.4
github.com/google/go-cmp@v0.6.0
github.com/google/uuid@v1.6.0
github.com/kisielk/errcheck@v1.5.0
github.com/kisielk/gotool@v1.0.0
github.com/pmezard/go-difflib@v1.0.0
github.com/stretchr/testify@v1.10.0
github.com/yuin/goldmark@v1.1.27
github.com/yuin/goldmark@v1.2.1
go.opentelemetry.io/collector/component@v0.116.0
go.opentelemetry.io/collector/config/configtelemetry@v0.116.0
go.opentelemetry.io/collector/extension/xextension@v0.116.0
go.opentelemetry.io/collector/extension@v0.116.0
go.opentelemetry.io/collector/pdata@v1.22.0
go.opentelemetry.io/otel/metric@v1.32.0
go.opentelemetry.io/otel/sdk/metric@v1.31.0
go.opentelemetry.io/otel/sdk@v1.31.0
go.opentelemetry.io/otel/trace@v1.32.0
go.opentelemetry.io/otel@v1.32.0
go.uber.org/goleak@v1.3.0
go.uber.org/multierr@v1.11.0
go.uber.org/zap@v1.27.0
golang.org/x/crypto@v0.0.0-20190308221718-c2843e01d9a2
golang.org/x/crypto@v0.0.0-20191011191535-87dc89f01550
golang.org/x/crypto@v0.0.0-20200622213623-75b288015ac9
golang.org/x/mod@v0.2.0
golang.org/x/mod@v0.3.0
golang.org/x/net@v0.0.0-20190404232315-eb5bcb51f2a3
golang.org/x/net@v0.0.0-20190620200207-3b0461eec859
golang.org/x/net@v0.0.0-20200226121028-0de0cce0169b
golang.org/x/net@v0.0.0-20201021035429-f5854403a974
golang.org/x/net@v0.33.0
golang.org/x/sync@v0.0.0-20190423024810-112230192c58
golang.org/x/sync@v0.0.0-20190911185100-cd5d95a43a6e
golang.org/x/sync@v0.0.0-20201020160332-67f06af15bc9
golang.org/x/sys@v0.0.0-20190215142949-d0b11bdaac8a
golang.org/x/sys@v0.0.0-20190412213103-97732733099d
golang.org/x/sys@v0.0.0-20200930185726-fdedc70b468f
golang.org/x/sys@v0.28.0
golang.org/x/text@v0.21.0
golang.org/x/text@v0.3.0
golang.org/x/text@v0.3.3
golang.org/x/tools@v0.0.0-20180917221912-90fa682c2a6e
golang.org/x/tools@v0.0.0-20191119224855-298f0cb1881e
golang.org/x/tools@v0.0.0-20200619180055-7c47624df98f
golang.org/x/tools@v0.0.0-20210106214847-113979e3529a
golang.org/x/xerrors@v0.0.0-20190717185122-a985d3407aa7
golang.org/x/xerrors@v0.0.0-20191011141410-1b5146add898
golang.org/x/xerrors@v0.0.0-20191204190536-9bdfabe68543
golang.org/x/xerrors@v0.0.0-20200804184101-5ec99f83aff1
google.golang.org/genproto/googleapis/rpc@v0.0.0-20241015192408-796eee8c2d53
google.golang.org/grpc@v1.69.2
google.golang.org/protobuf@v1.36.1
gopkg.in/yaml.v3@v3.0.1
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
