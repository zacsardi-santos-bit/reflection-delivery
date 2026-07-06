FROM golang:1.22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/authzed/spicedb.git && \
    git fetch --depth 30 origin 7c1078d0ee0bebe7df0cd1fc8c881e90a0fc2c83 && \
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


# --- offline dep-baking: warm Go module cache at build (network available here) ---
RUN cd /workspace/repo && \
    for d in $(find . -name go.mod -not -path '*/vendor/*' -exec dirname {} \; | sort -u); do \
      ( cd "$d" && echo "warming $d" && \
        (go mod download all 2>/dev/null || go mod download 2>/dev/null || true) && \
        (go test -count=1 -run '^$' ./... 2>/dev/null || true) ) ; \
    done; true
# warm exact dep versions the fix/test introduce (module coordinates only, not solution code)
RUN cd /workspace/repo && for m in \
    buf.build/gen/go/prometheus/prometheus/protocolbuffers/go@v1.34.2-20240704212942-bbf0a43bf797.2 \
    cloud.google.com/go/auth@v0.7.2 \
    cloud.google.com/go/iam@v1.1.10 \
    cloud.google.com/go/longrunning@v0.5.9 \
    cloud.google.com/go/spanner@v1.65.0 \
    github.com/Antonboom/testifylint@v1.3.1 \
    github.com/BurntSushi/toml@v1.4.0 \
    github.com/Crocmagnon/fatcontext@v0.2.2 \
    github.com/GaijinEntertainment/go-exhaustruct/v3@v3.2.0 \
    github.com/Microsoft/go-winio@v0.6.1 \
    github.com/Yiling-J/theine-go@v0.3.2 \
    github.com/authzed/authzed-go@v0.14.0 \
    github.com/aws/aws-sdk-go-v2/config@v1.27.27 \
    github.com/aws/aws-sdk-go-v2/credentials@v1.17.27 \
    github.com/aws/aws-sdk-go-v2/feature/ec2/imds@v1.16.11 \
    github.com/aws/aws-sdk-go-v2/feature/rds/auth@v1.4.15 \
    github.com/aws/aws-sdk-go-v2/internal/configsources@v1.3.15 \
    github.com/aws/aws-sdk-go-v2/internal/endpoints/v2@v2.6.15 \
    github.com/aws/aws-sdk-go-v2/internal/ini@v1.8.0 \
    github.com/aws/aws-sdk-go-v2/service/internal/accept-encoding@v1.11.3 \
    github.com/aws/aws-sdk-go-v2/service/internal/presigned-url@v1.11.17 \
    github.com/aws/aws-sdk-go-v2/service/sso@v1.22.4 \
    github.com/aws/aws-sdk-go-v2/service/ssooidc@v1.26.4 \
    github.com/aws/aws-sdk-go-v2/service/sts@v1.30.3 \
    github.com/aws/aws-sdk-go-v2@v1.30.3 \
    github.com/aws/smithy-go@v1.20.3 \
    github.com/bombsimon/wsl/v4@v4.2.1 \
    github.com/containerd/continuity@v0.3.0 \
    github.com/creasty/defaults@v1.7.0 \
    github.com/docker/cli@v25.0.2+incompatible \
    github.com/envoyproxy/go-control-plane@v0.12.0 \
    github.com/envoyproxy/protoc-gen-validate@v1.0.4 \
    github.com/evanphx/json-patch@v4.12.0+incompatible \
    github.com/go-openapi/swag@v0.22.3 \
    github.com/go-task/slim-sprig@v0.0.0-20230315185526-52ccab3ef572 \
    github.com/gofrs/flock@v0.8.1 \
    github.com/golangci/golangci-lint@v1.59.1 \
    github.com/google/pprof@v0.0.0-20240424215950-a892ee059fd6 \
    github.com/google/s2a-go@v0.1.7 \
    github.com/grpc-ecosystem/grpc-gateway/v2@v2.21.0 \
    github.com/imdario/mergo@v0.3.12 \
    github.com/jjti/go-spancheck@v0.6.1 \
    github.com/klauspost/compress@v1.17.8 \
    github.com/maypok86/otter@v1.2.1 \
    github.com/mgechev/revive@v1.3.7 \
    github.com/moricho/tparallel@v0.3.1 \
    github.com/onsi/ginkgo/v2@v2.17.3 \
    github.com/opencontainers/image-spec@v1.1.0-rc5 \
    github.com/opencontainers/runc@v1.1.12 \
    github.com/ory/dockertest/v3@v3.10.0 \
    github.com/polyfloyd/go-errorlint@v1.5.2 \
    github.com/prometheus/client_golang@v1.19.1 \
    github.com/prometheus/common@v0.55.0 \
    github.com/rs/cors@v1.11.0 \
    github.com/rs/xid@v1.5.0 \
    github.com/ryancurrah/gomodguard@v1.3.2 \
    github.com/samber/lo@v1.46.0 \
    github.com/sashamelentyev/usestdlibvars@v1.26.0 \
    github.com/schollz/progressbar/v3@v3.14.5 \
    github.com/sivchari/tenv@v1.7.1 \
    github.com/t-yuki/gocover-cobertura@v0.0.0-20180217150009-aaee18c8195c \
    github.com/uudashr/gocognit@v1.1.2 \
    github.com/xeipuuv/gojsonpointer@v0.0.0-20180127040702-4e3ac2762d5f \
    go-simpler.org/sloglint@v0.7.1 \
    go.opentelemetry.io/contrib/instrumentation/google.golang.org/grpc/otelgrpc@v0.53.0 \
    go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp@v0.53.0 \
    go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc@v1.19.0 \
    go.opentelemetry.io/otel/exporters/otlp/otlptrace@v1.22.0 \
    go.opentelemetry.io/otel/metric@v1.28.0 \
    go.opentelemetry.io/otel/sdk@v1.28.0 \
    go.opentelemetry.io/otel/trace@v1.28.0 \
    go.opentelemetry.io/otel@v1.28.0 \
    go.opentelemetry.io/proto/otlp@v1.0.0 \
    golang.org/x/crypto@v0.25.0 \
    golang.org/x/mod@v0.19.0 \
    golang.org/x/net@v0.27.0 \
    golang.org/x/oauth2@v0.21.0 \
    golang.org/x/sync@v0.7.0 \
    golang.org/x/sys@v0.0.0-20220702020025-31831981b65f \
    golang.org/x/sys@v0.22.0 \
    golang.org/x/term@v0.22.0 \
    golang.org/x/text@v0.16.0 \
    golang.org/x/time@v0.5.0 \
    golang.org/x/tools@v0.1.11 \
    golang.org/x/tools@v0.23.0 \
    golang.org/x/xerrors@v0.0.0-20231012003039-104605ab7028 \
    google.golang.org/api@v0.189.0 \
    google.golang.org/genproto/googleapis/api@v0.0.0-20240723171418-e6d459c13d2a \
    google.golang.org/genproto/googleapis/rpc@v0.0.0-20240723171418-e6d459c13d2a \
    google.golang.org/genproto@v0.0.0-20240722135656-d784300faade \
    google.golang.org/grpc@v1.65.0 \
    gotest.tools/v3@v3.3.0 \
    honnef.co/go/tools@v0.4.7 \
    k8s.io/api@v0.30.1 \
    k8s.io/apiextensions-apiserver@v0.30.1 \
    k8s.io/apimachinery@v0.30.1 \
    k8s.io/client-go@v0.30.1 \
    k8s.io/klog/v2@v2.120.1 \
    k8s.io/utils@v0.0.0-20230726121419-3b25d923346b \
    sigs.k8s.io/controller-runtime@v0.18.4 \
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
