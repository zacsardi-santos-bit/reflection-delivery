FROM golang:1.22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/akuity/kargo.git && \
    git fetch --depth 30 origin 9f24ba3c94f69bfeab4de5c9d1fccd069ca5659f && \
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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./internal/controller/promotions 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
github.com/cenkalti/backoff/v4@v4.3.0
github.com/davecgh/go-spew@v1.1.2-0.20180830191138-d8f796af33cc
github.com/fxamacker/cbor/v2@v2.7.0
github.com/go-openapi/swag@v0.22.4
github.com/go-task/slim-sprig/v3@v3.0.0
github.com/google/pprof@v0.0.0-20240525223248-4bfdf5a9a2af
github.com/grpc-ecosystem/grpc-gateway/v2@v2.20.0
github.com/onsi/ginkgo/v2@v2.19.0
github.com/onsi/gomega@v1.33.1
github.com/pmezard/go-difflib@v1.0.1-0.20181226105442-5d4384ee4fb2
github.com/prometheus/client_golang@v1.19.1
github.com/prometheus/client_model@v0.6.1
github.com/prometheus/common@v0.55.0
github.com/prometheus/procfs@v0.15.1
github.com/rogpeppe/go-internal@v1.12.0
github.com/x448/float16@v0.8.4
go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp@v0.53.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc@v1.27.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace@v1.28.0
go.opentelemetry.io/otel/metric@v1.28.0
go.opentelemetry.io/otel/sdk@v1.28.0
go.opentelemetry.io/otel/trace@v1.28.0
go.opentelemetry.io/otel@v1.28.0
go.opentelemetry.io/proto/otlp@v1.3.1
gopkg.in/evanphx/json-patch.v4@v4.12.0
k8s.io/api@v0.31.0
k8s.io/apiextensions-apiserver@v0.31.0
k8s.io/apimachinery@v0.31.0
k8s.io/apiserver@v0.31.0
k8s.io/client-go@v0.31.0
k8s.io/utils@v0.0.0-20240711033017-18e509b52bc8
sigs.k8s.io/controller-runtime@v0.19.0
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
