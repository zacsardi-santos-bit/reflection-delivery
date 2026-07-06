FROM golang:1.23

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/prometheus/prometheus.git && \
    git fetch --depth 30 origin f141e8ad734ccc85df66d5a081ca58b348ed43dd && \
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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./storage/remote/otlptranslator/prometheusremotewrite ./web/api/v1 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
buf.build/gen/go/bufbuild/bufplugin/protocolbuffers/go@v1.36.4-20250121211742-6d880cc6cc8d.1
buf.build/gen/go/bufbuild/protovalidate/protocolbuffers/go@v1.36.4-20241127180247-a33202765966.1
buf.build/gen/go/bufbuild/registry/protocolbuffers/go@v1.36.4-20250116203702-1c024d64352b.1
buf.build/go/bufplugin@v0.7.0
cel.dev/expr@v0.19.2
connectrpc.com/otelconnect@v0.7.1
github.com/bufbuild/buf@v1.50.1
github.com/bufbuild/protovalidate-go@v0.8.2
github.com/digitalocean/godo@v1.141.0
github.com/docker/docker@v28.0.0+incompatible
github.com/docker/docker@v28.0.2+incompatible
github.com/envoyproxy/protoc-gen-validate@v1.1.0
github.com/hashicorp/consul/api@v1.31.2
github.com/hetznercloud/hcloud-go/v2@v2.20.1
github.com/ionos-cloud/sdk-go/v6@v6.3.2
github.com/knadh/koanf/maps@v0.1.1
github.com/open-telemetry/opentelemetry-collector-contrib/internal/exp/metrics@v0.122.0
github.com/open-telemetry/opentelemetry-collector-contrib/pkg/pdatatest@v0.122.0
github.com/open-telemetry/opentelemetry-collector-contrib/pkg/pdatautil@v0.122.0
github.com/open-telemetry/opentelemetry-collector-contrib/processor/deltatocumulativeprocessor@v0.122.0
github.com/quic-go/quic-go@v0.50.0
github.com/scaleway/scaleway-sdk-go@v1.0.0-beta.32
go.opentelemetry.io/collector/component/componentstatus@v0.122.1
go.opentelemetry.io/collector/component/componenttest@v0.122.1
go.opentelemetry.io/collector/component@v1.28.1
go.opentelemetry.io/collector/confmap/xconfmap@v0.122.0
go.opentelemetry.io/collector/confmap@v1.28.0
go.opentelemetry.io/collector/consumer/consumertest@v0.122.1
go.opentelemetry.io/collector/consumer/xconsumer@v0.122.1
go.opentelemetry.io/collector/consumer@v1.28.1
go.opentelemetry.io/collector/featuregate@v1.28.0
go.opentelemetry.io/collector/pdata/pprofile@v0.122.1
go.opentelemetry.io/collector/pdata/testdata@v0.122.1
go.opentelemetry.io/collector/pdata@v1.28.1
go.opentelemetry.io/collector/pipeline@v0.122.1
go.opentelemetry.io/collector/processor/processortest@v0.122.1
go.opentelemetry.io/collector/processor/xprocessor@v0.122.1
go.opentelemetry.io/collector/processor@v0.122.1
go.opentelemetry.io/collector/semconv@v0.122.1
golang.org/x/crypto@v0.36.0
golang.org/x/net@v0.37.0
golang.org/x/sync@v0.12.0
golang.org/x/sys@v0.31.0
golang.org/x/term@v0.30.0
golang.org/x/text@v0.23.0
golang.org/x/tools@v0.31.0
google.golang.org/grpc@v1.71.0
google.golang.org/protobuf@v1.36.5
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
