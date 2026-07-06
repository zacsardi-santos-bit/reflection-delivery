FROM golang:1.23

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/open-telemetry/opentelemetry-collector.git && \
    git fetch --depth 30 origin f9bd8ca5d132f19bcf6654f4328651450393e267 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/service

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
RUN set +e; cd /workspace/repo/service 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./internal/promtest ./telemetry ./telemetry/internal/migration 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
github.com/google/go-cmp@v0.7.0
github.com/grpc-ecosystem/grpc-gateway/v2@v2.26.1
github.com/prometheus/client_golang@v1.21.1
go.opentelemetry.io/contrib/bridges/otelzap@v0.10.0
go.opentelemetry.io/contrib/instrumentation/google.golang.org/grpc/otelgrpc@v0.60.0
go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp@v0.60.0
go.opentelemetry.io/contrib/otelconf@v0.15.0
go.opentelemetry.io/contrib/propagators/b3@v1.35.0
go.opentelemetry.io/contrib/zpages@v0.60.0
go.opentelemetry.io/otel/exporters/otlp/otlplog/otlploggrpc@v0.11.0
go.opentelemetry.io/otel/exporters/otlp/otlplog/otlploghttp@v0.11.0
go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetricgrpc@v1.35.0
go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetrichttp@v1.35.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc@v1.35.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp@v1.35.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace@v1.35.0
go.opentelemetry.io/otel/exporters/prometheus@v0.57.0
go.opentelemetry.io/otel/exporters/stdout/stdoutlog@v0.11.0
go.opentelemetry.io/otel/exporters/stdout/stdoutmetric@v1.35.0
go.opentelemetry.io/otel/exporters/stdout/stdouttrace@v1.35.0
go.opentelemetry.io/otel/log@v0.11.0
go.opentelemetry.io/otel/metric@v1.35.0
go.opentelemetry.io/otel/sdk/log@v0.11.0
go.opentelemetry.io/otel/sdk/metric@v1.35.0
go.opentelemetry.io/otel/sdk@v1.35.0
go.opentelemetry.io/otel/trace@v1.35.0
go.opentelemetry.io/otel@v1.35.0
golang.org/x/net@v0.35.0
golang.org/x/sys@v0.30.0
golang.org/x/text@v0.22.0
google.golang.org/genproto/googleapis/api@v0.0.0-20250218202821-56aae31c358a
google.golang.org/genproto/googleapis/rpc@v0.0.0-20250218202821-56aae31c358a
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
