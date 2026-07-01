FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/flyteorg/flyte.git && \
    git fetch --depth 30 origin 2334d3f9464518616f82bb4a706dc4ce981ca5a8 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo/flytestdlib

RUN go mod download

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
RUN set +e; cd /workspace/repo/flytestdlib 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./otelutils 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
github.com/cenkalti/backoff/v4@v4.2.1
github.com/emicklei/go-restful/v3@v3.11.0
github.com/emicklei/go-restful/v3@v3.9.0
github.com/go-jose/go-jose/v3@v3.0.0
github.com/go-logr/logr@v1.4.1
github.com/go-sql-driver/mysql@v1.6.0
github.com/grpc-ecosystem/grpc-gateway/v2@v2.19.0
github.com/imdario/mergo@v0.3.6
github.com/jackc/pgservicefile@v0.0.0-20221227161230-091c0ba34f0a
github.com/mattn/go-isatty@v0.0.14
github.com/rogpeppe/go-internal@v1.10.0
github.com/sendgrid/rest@v2.6.9+incompatible
github.com/spf13/afero@v1.8.2
go.opentelemetry.io/contrib/instrumentation/google.golang.org/grpc/otelgrpc@v0.47.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc@v1.24.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp@v1.24.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace@v1.24.0
go.opentelemetry.io/otel/exporters/stdout/stdouttrace@v1.22.0
go.opentelemetry.io/otel/metric@v1.24.0
go.opentelemetry.io/otel/sdk@v1.24.0
go.opentelemetry.io/otel/trace@v1.24.0
go.opentelemetry.io/otel@v1.24.0
go.opentelemetry.io/proto/otlp@v1.1.0
go.uber.org/goleak@v1.3.0
golang.org/x/exp@v0.0.0-20220722155223-a9213eeb770e
golang.org/x/net@v0.22.0
gorm.io/gorm@v1.25.4
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
