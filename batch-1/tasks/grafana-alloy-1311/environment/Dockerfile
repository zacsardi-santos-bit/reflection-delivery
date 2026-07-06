FROM golang:1.22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/grafana/alloy.git && \
    git fetch --depth 30 origin beb5037c9e270944a6d96bee0909048145cf1b12 && \
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

RUN go build ./internal/service/cluster/... || true


# --- offline dep-baking: warm Go module cache at build (network available here) ---
RUN cd /workspace/repo && \
    for d in $(find . -name go.mod -not -path '*/vendor/*' -exec dirname {} \; | sort -u); do \
      ( cd "$d" && echo "warming $d" && \
        (go mod download all 2>/dev/null || go mod download 2>/dev/null || true) && \
        (go test -count=1 -run '^$' ./... 2>/dev/null || true) ) ; \
    done; true
# warm exact dep versions the fix/test introduce (module coordinates only, not solution code)
RUN cd /workspace/repo && for m in \
    cloud.google.com/go/auth@v0.4.1 \
    cloud.google.com/go/compute/metadata@v0.3.0 \
    cloud.google.com/go/iam@v1.1.6 \
    cloud.google.com/go/kms@v1.15.7 \
    cloud.google.com/go/pubsub@v1.36.1 \
    cloud.google.com/go@v0.112.2 \
    github.com/Azure/azure-sdk-for-go/sdk/resourcemanager/compute/armcompute/v5@v5.5.0 \
    github.com/Code-Hex/go-generics-cache@v1.3.1 \
    github.com/GoogleCloudPlatform/opentelemetry-operations-go/detectors/gcp@v1.23.0 \
    github.com/VividCortex/gohistogram@v1.0.0 \
    github.com/aws/aws-sdk-go@v1.53.11 \
    github.com/cncf/xds/go@v0.0.0-20240318125728-8a4994d93e50 \
    github.com/cpuguy83/go-md2man/v2@v2.0.3 \
    github.com/digitalocean/godo@v1.109.0 \
    github.com/distribution/reference@v0.5.0 \
    github.com/docker/docker@v25.0.5+incompatible \
    github.com/emicklei/go-restful/v3@v3.11.0 \
    github.com/go-logr/logr@v1.4.1 \
    github.com/go-openapi/jsonpointer@v0.20.2 \
    github.com/go-openapi/jsonreference@v0.20.4 \
    github.com/go-openapi/swag@v0.22.9 \
    github.com/go-resty/resty/v2@v2.12.0 \
    github.com/go-task/slim-sprig@v0.0.0-20230315185526-52ccab3ef572 \
    github.com/google/pprof@v0.0.0-20240227163752-401108e1b7e7 \
    github.com/gophercloud/gophercloud@v1.8.0 \
    github.com/gorilla/handlers@v1.5.1 \
    github.com/grafana/beyla@v1.6.3 \
    github.com/grafana/cadvisor@v0.0.0-20231110094609-5f7917925dea \
    github.com/grafana/opentelemetry-collector/otelcol@v0.0.0-20240429170914-d1e101852ba5 \
    github.com/grafana/opentelemetry-collector/service@v0.0.0-20240429170914-d1e101852ba5 \
    github.com/grafana/opentelemetry-collector@v0.4.1-0.20240429170914-d1e101852ba5 \
    github.com/grafana/regexp@v0.0.0-20221123153739-15dc172cd2db \
    github.com/hashicorp/consul/api@v1.28.3 \
    github.com/hashicorp/consul/sdk@v0.15.0 \
    github.com/hashicorp/nomad/api@v0.0.0-20240306004928-3e7191ccb702 \
    github.com/hetznercloud/hcloud-go/v2@v2.6.0 \
    github.com/ianlancetaylor/demangle@v0.0.0-20230524184225-eabc099b10ab \
    github.com/jaegertracing/jaeger@v1.57.0 \
    github.com/klauspost/compress@v1.17.8 \
    github.com/linode/linodego@v1.33.0 \
    github.com/lufia/plan9stats@v0.0.0-20211012122336-39d0f177ccd0 \
    github.com/miekg/dns@v1.1.58 \
    github.com/mitchellh/hashstructure@v1.1.0 \
    github.com/onsi/ginkgo/v2@v2.14.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/connector/servicegraphconnector@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/connector/spanmetricsconnector@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/exporter/awss3exporter@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/exporter/kafkaexporter@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/exporter/loadbalancingexporter@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/exporter/prometheusexporter@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/extension/basicauthextension@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/extension/bearertokenauthextension@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/extension/headerssetterextension@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/extension/jaegerremotesampling@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/extension/oauth2clientauthextension@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/extension/sigv4authextension@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/aws/ecsutil@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/common@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/coreinternal@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/exp/metrics@v0.100.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/exp/metrics@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/filter@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/k8sconfig@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/k8stest@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/kafka@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/metadataproviders@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/sharedcomponent@v0.100.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/internal/sharedcomponent@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/batchpersignal@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/golden@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/ottl@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/pdatatest@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/pdatautil@v0.100.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/pdatautil@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/resourcetotelemetry@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/sampling@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/translator/azure@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/translator/jaeger@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/translator/loki@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/translator/opencensus@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/translator/prometheus@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/pkg/translator/zipkin@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/processor/attributesprocessor@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/processor/deltatocumulativeprocessor@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/processor/filterprocessor@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/processor/k8sattributesprocessor@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/processor/probabilisticsamplerprocessor@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/processor/resourcedetectionprocessor@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/processor/spanprocessor@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/processor/tailsamplingprocessor@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/processor/transformprocessor@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/receiver/filestatsreceiver@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/receiver/jaegerreceiver@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/receiver/kafkareceiver@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/receiver/opencensusreceiver@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/receiver/vcenterreceiver@v0.102.0 \
    github.com/open-telemetry/opentelemetry-collector-contrib/receiver/zipkinreceiver@v0.102.0 \
    github.com/ovh/go-ovh@v1.4.3 \
    github.com/pelletier/go-toml/v2@v2.1.0 \
    github.com/power-devops/perfstat@v0.0.0-20210106213030-5aafc221ea8c \
    github.com/prometheus/common@v0.53.0 \
    github.com/prometheus/procfs@v0.15.0 \
    github.com/prometheus/prometheus@v0.51.2 \
    github.com/rs/cors@v1.10.1 \
    github.com/scaleway/scaleway-sdk-go@v1.0.0-beta.25 \
    github.com/shirou/gopsutil/v3@v3.24.4 \
    github.com/shirou/gopsutil@v3.21.11+incompatible \
    github.com/shoenig/test@v0.6.4 \
    github.com/spf13/cobra@v1.8.0 \
    github.com/spf13/viper@v1.18.2 \
    github.com/tklauser/go-sysconf@v0.3.12 \
    github.com/tklauser/numcpus@v0.6.1 \
    github.com/vmware/govmomi@v0.36.3 \
    go.einride.tech/aip@v0.66.0 \
    go.etcd.io/etcd/api/v3@v3.5.10 \
    go.etcd.io/etcd/client/pkg/v3@v3.5.10 \
    go.etcd.io/etcd/client/v3@v3.5.10 \
    go.opentelemetry.io/collector/component@v0.102.0 \
    go.opentelemetry.io/collector/component@v0.102.1 \
    go.opentelemetry.io/collector/config/configauth@v0.102.0 \
    go.opentelemetry.io/collector/config/configauth@v0.102.1 \
    go.opentelemetry.io/collector/config/configcompression@v1.9.0 \
    go.opentelemetry.io/collector/config/configgrpc@v0.102.1 \
    go.opentelemetry.io/collector/config/confighttp@v0.102.0 \
    go.opentelemetry.io/collector/config/confighttp@v0.102.1 \
    go.opentelemetry.io/collector/config/confignet@v0.102.1 \
    go.opentelemetry.io/collector/config/configopaque@v1.9.0 \
    go.opentelemetry.io/collector/config/configretry@v0.102.1 \
    go.opentelemetry.io/collector/config/configtelemetry@v0.102.0 \
    go.opentelemetry.io/collector/config/configtelemetry@v0.102.1 \
    go.opentelemetry.io/collector/config/configtls@v0.102.0 \
    go.opentelemetry.io/collector/config/configtls@v0.102.1 \
    go.opentelemetry.io/collector/config/internal@v0.102.0 \
    go.opentelemetry.io/collector/config/internal@v0.102.1 \
    go.opentelemetry.io/collector/confmap/converter/expandconverter@v0.102.1 \
    go.opentelemetry.io/collector/confmap/provider/envprovider@v0.102.1 \
    go.opentelemetry.io/collector/confmap/provider/fileprovider@v0.102.1 \
    go.opentelemetry.io/collector/confmap/provider/httpprovider@v0.102.1 \
    go.opentelemetry.io/collector/confmap/provider/httpsprovider@v0.102.1 \
    go.opentelemetry.io/collector/confmap/provider/yamlprovider@v0.102.1 \
    go.opentelemetry.io/collector/confmap@v0.102.0 \
    go.opentelemetry.io/collector/confmap@v0.102.1 \
    go.opentelemetry.io/collector/connector@v0.102.1 \
    go.opentelemetry.io/collector/consumer@v0.102.0 \
    go.opentelemetry.io/collector/consumer@v0.102.1 \
    go.opentelemetry.io/collector/exporter/otlpexporter@v0.102.1 \
    go.opentelemetry.io/collector/exporter/otlphttpexporter@v0.102.1 \
    go.opentelemetry.io/collector/exporter@v0.102.1 \
    go.opentelemetry.io/collector/extension/auth@v0.102.0 \
    go.opentelemetry.io/collector/extension/auth@v0.102.1 \
    go.opentelemetry.io/collector/extension/zpagesextension@v0.99.0 \
    go.opentelemetry.io/collector/extension@v0.102.0 \
    go.opentelemetry.io/collector/extension@v0.102.1 \
    go.opentelemetry.io/collector/featuregate@v1.9.0 \
    go.opentelemetry.io/collector/filter@v0.102.1 \
    go.opentelemetry.io/collector/otelcol@v0.102.1 \
    go.opentelemetry.io/collector/pdata/testdata@v0.102.0 \
    go.opentelemetry.io/collector/pdata/testdata@v0.102.1 \
    go.opentelemetry.io/collector/pdata@v1.9.0 \
    go.opentelemetry.io/collector/processor/batchprocessor@v0.102.1 \
    go.opentelemetry.io/collector/processor/memorylimiterprocessor@v0.102.1 \
    go.opentelemetry.io/collector/processor@v0.102.1 \
    go.opentelemetry.io/collector/receiver/otlpreceiver@v0.102.1 \
    go.opentelemetry.io/collector/receiver@v0.102.1 \
    go.opentelemetry.io/collector/semconv@v0.100.1-0.20240509190532-c555005fcc80 \
    go.opentelemetry.io/collector/semconv@v0.102.1 \
    go.opentelemetry.io/collector/service@v0.102.1 \
    go.opentelemetry.io/collector@v0.102.0 \
    go.opentelemetry.io/collector@v0.102.1 \
    go.opentelemetry.io/contrib/config@v0.7.0 \
    go.opentelemetry.io/contrib/instrumentation/google.golang.org/grpc/otelgrpc@v0.52.0 \
    go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp@v0.52.0 \
    go.opentelemetry.io/contrib/propagators/b3@v1.27.0 \
    go.opentelemetry.io/contrib/zpages@v0.50.0 \
    go.opentelemetry.io/otel/bridge/opencensus@v1.27.0 \
    go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetricgrpc@v1.27.0 \
    go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetrichttp@v1.27.0 \
    go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc@v1.27.0 \
    go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp@v1.27.0 \
    go.opentelemetry.io/otel/exporters/otlp/otlptrace@v1.27.0 \
    go.opentelemetry.io/otel/exporters/prometheus@v0.49.0 \
    go.opentelemetry.io/otel/exporters/stdout/stdoutmetric@v1.27.0 \
    go.opentelemetry.io/otel/exporters/stdout/stdouttrace@v1.27.0 \
    go.opentelemetry.io/otel/metric@v1.27.0 \
    go.opentelemetry.io/otel/sdk/metric@v1.27.0 \
    go.opentelemetry.io/otel/sdk@v1.27.0 \
    go.opentelemetry.io/otel/trace@v1.27.0 \
    go.opentelemetry.io/otel@v1.27.0 \
    go.opentelemetry.io/proto/otlp@v1.2.0 \
    golang.org/x/crypto@v0.21.0 \
    golang.org/x/crypto@v0.24.0 \
    golang.org/x/mod@v0.17.0 \
    golang.org/x/net@v0.22.0 \
    golang.org/x/net@v0.25.0 \
    golang.org/x/net@v0.26.0 \
    golang.org/x/oauth2@v0.18.0 \
    golang.org/x/oauth2@v0.20.0 \
    golang.org/x/sys@v0.11.0 \
    golang.org/x/sys@v0.18.0 \
    golang.org/x/sys@v0.19.0 \
    golang.org/x/sys@v0.20.0 \
    golang.org/x/sys@v0.21.0 \
    golang.org/x/term@v0.18.0 \
    golang.org/x/term@v0.21.0 \
    golang.org/x/text@v0.15.0 \
    golang.org/x/tools@v0.21.1-0.20240508182429-e35e4ccd0d2d \
    google.golang.org/api@v0.180.0 \
    google.golang.org/genproto/googleapis/api@v0.0.0-20240520151616-dc85e6b867a5 \
    google.golang.org/genproto/googleapis/rpc@v0.0.0-20240520151616-dc85e6b867a5 \
    google.golang.org/genproto@v0.0.0-20240227224415-6ceb2ff114de \
    google.golang.org/grpc@v1.64.0 \
    google.golang.org/protobuf@v1.34.1 \
    k8s.io/api@v0.29.3 \
    k8s.io/apimachinery@v0.29.3 \
    k8s.io/client-go@v0.29.3 \
    k8s.io/klog/v2@v2.120.1 \
    k8s.io/kube-openapi@v0.0.0-20231010175941-2dd684a91f00 \
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
