FROM golang:1.24

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN mv /usr/bin/git /usr/bin/git_real

RUN printf '#!/bin/sh\nif [ "$1" = "apply" ]; then\n  shift\n  exec /usr/bin/git_real apply --exclude="docs/operations/images/double-nat.png" "$@"\nfi\nexec /usr/bin/git_real "$@"\n' > /usr/bin/git && chmod +x /usr/bin/git

RUN curl -L -o /tmp/prometheus.tar.gz "https://github.com/prometheus/prometheus/releases/download/v3.5.0/prometheus-3.5.0.linux-amd64.tar.gz" && \
    tar -xzf /tmp/prometheus.tar.gz -C /tmp/ && \
    mv /tmp/prometheus-3.5.0.linux-amd64/promtool /usr/local/bin/promtool && \
    chmod +x /usr/local/bin/promtool && \
    rm -rf /tmp/prometheus.tar.gz /tmp/prometheus-3.5.0.linux-amd64

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/gardener/gardener.git && \
    git fetch --depth 30 origin f6fb7e2ca019fdd2a09c0a5da6475bf5d6bd2430 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./pkg/component/observability/logging/vali ./pkg/component/observability/opentelemetry/collector ./pkg/gardenlet/operation/botanist ./pkg/utils/gardener 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
cloud.google.com/go/auth/oauth2adapt@v0.2.4
cloud.google.com/go/auth@v0.9.4
cloud.google.com/go/compute@v1.29.0
cloud.google.com/go@v0.116.0
dario.cat/mergo@v1.0.1
github.com/alecthomas/units@v0.0.0-20240626203959-61d1e3462e30
github.com/cenkalti/backoff/v4@v4.3.0
github.com/cncf/xds/go@v0.0.0-20250121191232-2f005788dc42
github.com/containerd/containerd@v1.7.27
github.com/coreos/go-systemd/v22@v22.5.0
github.com/digitalocean/godo@v1.125.0
github.com/docker/cli@v28.3.2+incompatible
github.com/docker/docker@v27.2.0+incompatible
github.com/gardener/machine-controller-manager@v0.59.0
github.com/go-kit/log@v0.2.1
github.com/go-logfmt/logfmt@v0.6.0
github.com/go-logr/logr@v1.4.2
github.com/go-resty/resty/v2@v2.13.1
github.com/go-viper/mapstructure/v2@v2.3.0
github.com/go-zookeeper/zk@v1.0.3
github.com/godbus/dbus/v5@v5.0.4
github.com/googleapis/gax-go/v2@v2.13.0
github.com/gophercloud/gophercloud@v1.14.0
github.com/grpc-ecosystem/grpc-gateway/v2@v2.25.1
github.com/hashicorp/consul/api@v1.29.4
github.com/hashicorp/nomad/api@v0.0.0-20240717122358-3d93bd3778f3
github.com/hetznercloud/hcloud-go/v2@v2.13.1
github.com/ionos-cloud/sdk-go/v6@v6.2.1
github.com/linode/linodego@v1.40.0
github.com/onsi/ginkgo/v2@v2.23.4
github.com/onsi/gomega@v1.37.0
github.com/open-telemetry/opentelemetry-operator@v0.119.0
github.com/operator-framework/operator-lib@v0.17.0
github.com/prometheus-operator/prometheus-operator/pkg/apis/monitoring@v0.83.0
github.com/prometheus/client_golang@v1.22.0
github.com/prometheus/common/sigv4@v0.1.0
github.com/prometheus/procfs@v0.16.1
github.com/prometheus/prometheus@v0.55.1
github.com/xeipuuv/gojsonpointer@v0.0.0-20180127040702-4e3ac2762d5f
github.com/xeipuuv/gojsonpointer@v0.0.0-20190905194746-02993c407bfb
github.com/xeipuuv/gojsonreference@v0.0.0-20180127040603-bd5ef7bd5415
github.com/xeipuuv/gojsonschema@v1.2.0
go.opentelemetry.io/collector/featuregate@v1.26.0
go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp@v0.58.0
go.opentelemetry.io/otel/exporters/otlp/otlplog/otlploggrpc@v0.8.0
go.opentelemetry.io/otel/exporters/otlp/otlplog/otlploghttp@v0.8.0
go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetricgrpc@v1.32.0
go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetrichttp@v1.34.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc@v1.33.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp@v1.32.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace@v1.33.0
go.opentelemetry.io/otel/exporters/prometheus@v0.56.0
go.opentelemetry.io/otel/exporters/stdout/stdoutlog@v0.8.0
go.opentelemetry.io/otel/exporters/stdout/stdoutmetric@v1.32.0
go.opentelemetry.io/otel/exporters/stdout/stdouttrace@v1.32.0
go.opentelemetry.io/otel/log@v0.8.0
go.opentelemetry.io/otel/metric@v1.36.0
go.opentelemetry.io/otel/sdk/log@v0.8.0
go.opentelemetry.io/otel/sdk/metric@v1.34.0
go.opentelemetry.io/otel/sdk@v1.35.0
go.opentelemetry.io/otel/trace@v1.36.0
go.opentelemetry.io/otel@v1.36.0
go.opentelemetry.io/proto/otlp@v1.5.0
go.uber.org/mock@v0.5.0
golang.org/x/crypto@v0.40.0
golang.org/x/exp@v0.0.0-20250711185948-6ae5c78190dc
golang.org/x/mod@v0.25.0
golang.org/x/mod@v0.26.0
golang.org/x/net@v0.42.0
golang.org/x/sync@v0.15.0
golang.org/x/sys@v0.0.0-20210119212857-b64e53b001e4
golang.org/x/sys@v0.34.0
golang.org/x/term@v0.33.0
golang.org/x/text@v0.27.0
golang.org/x/tools/go/expect@v0.1.0-deprecated
golang.org/x/tools@v0.1.0
golang.org/x/tools@v0.34.0
golang.org/x/tools@v0.35.0
google.golang.org/api@v0.198.0
google.golang.org/genproto/googleapis/api@v0.0.0-20250512202823-5a2f75b736a9
google.golang.org/genproto@v0.0.0-20241118233622-e639e219e697
google.golang.org/grpc@v1.72.2
gotest.tools/v3@v3.4.0
helm.sh/helm/v3@v3.18.4
istio.io/api@v1.25.3
k8s.io/api@v0.33.3
k8s.io/apiextensions-apiserver@v0.33.3
k8s.io/apimachinery@v0.33.3
k8s.io/apiserver@v0.33.3
k8s.io/cli-runtime@v0.33.3
k8s.io/client-go@v0.33.3
k8s.io/cluster-bootstrap@v0.33.3
k8s.io/code-generator@v0.33.3
k8s.io/component-base@v0.33.3
k8s.io/component-helpers@v0.33.3
k8s.io/kms@v0.33.3
k8s.io/kube-aggregator@v0.33.3
k8s.io/kube-proxy@v0.33.3
k8s.io/kubelet@v0.33.3
k8s.io/metrics@v0.33.3
k8s.io/pod-security-admission@v0.33.3
k8s.io/utils@v0.0.0-20250604170112-4c0f3b243397
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
