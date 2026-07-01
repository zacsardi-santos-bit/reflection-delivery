FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/kubeflow/pipelines.git && \
    git fetch --depth 30 origin 87bdb7c3b1126ae5e899826be0834c11764edbae && \
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

RUN go build ./backend/... 2>&1 | tail -5 || true

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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./backend/src/agent/persistence/worker ./backend/src/apiserver/storage ./backend/src/common/util ./backend/src/crd/controller/scheduledworkflow/util ./backend/src/v2/driver 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
cloud.google.com/go/firestore@v1.5.0
cloud.google.com/go/pubsub@v1.10.3
cloud.google.com/go/storage@v1.15.0
contrib.go.opencensus.io/exporter/stackdriver@v0.13.5
github.com/Azure/azure-sdk-for-go@v51.1.0+incompatible
github.com/Azure/azure-sdk-for-go@v54.0.0+incompatible
github.com/Azure/azure-service-bus-go@v0.10.11
github.com/Azure/go-amqp@v0.13.4
github.com/Azure/go-amqp@v0.13.7
github.com/Azure/go-autorest/autorest/adal@v0.5.0
github.com/Azure/go-autorest/autorest/adal@v0.9.11
github.com/Azure/go-autorest/autorest/azure/auth@v0.5.7
github.com/Azure/go-autorest/autorest/date@v0.1.0
github.com/Azure/go-autorest/autorest/mocks@v0.1.0
github.com/Azure/go-autorest/autorest/mocks@v0.2.0
github.com/Azure/go-autorest/autorest@v0.11.17
github.com/Azure/go-autorest/autorest@v0.9.0
github.com/Azure/go-autorest/logger@v0.1.0
github.com/Azure/go-autorest/tracing@v0.5.0
github.com/GoogleCloudPlatform/cloudsql-proxy@v1.22.0
github.com/Microsoft/hcsshim@v0.11.4
github.com/ProtonMail/go-crypto@v1.0.0
github.com/PuerkitoBio/purell@v1.0.0
github.com/PuerkitoBio/urlesc@v0.0.0-20160726150825-5bd2802263f2
github.com/akavel/rsrc@v0.10.2
github.com/antlr/antlr4/runtime/Go/antlr/v4@v4.0.0-20230305170008-8188dc5388df
github.com/aws/aws-sdk-go@v1.38.35
github.com/bytedance/sonic@v1.5.0
github.com/bytedance/sonic@v1.9.1
github.com/cenkalti/backoff/v4@v4.2.1
github.com/chenzhuoyu/base64x@v0.0.0-20211019084208-fb5309c8db06
github.com/chenzhuoyu/base64x@v0.0.0-20221115062448-fe3a3abad311
github.com/cloudevents/sdk-go/v2@v2.15.1
github.com/cloudflare/circl@v1.3.7
github.com/containerd/containerd@v1.7.13
github.com/coreos/go-systemd/v22@v22.3.1
github.com/docker/spdystream@v0.0.0-20160310174837-449fdfce4d96
github.com/elazarl/goproxy@v0.0.0-20170405201442-c4fc26588b6e
github.com/emicklei/go-restful/v3@v3.11.0
github.com/evanphx/json-patch/v5@v5.8.0
github.com/evanphx/json-patch@v4.2.0+incompatible
github.com/fsnotify/fsnotify@v1.7.0
github.com/gabriel-vasile/mimetype@v1.4.2
github.com/gin-gonic/gin@v1.9.1
github.com/gliderlabs/ssh@v0.3.7
github.com/go-git/go-git-fixtures/v4@v4.3.2-0.20231010084843-55a94097c399
github.com/go-git/go-git/v5@v5.12.0
github.com/go-logr/logr@v1.4.1
github.com/go-logr/zapr@v1.3.0
github.com/go-openapi/jsonpointer@v0.0.0-20160704185906-46af16f9f7b1
github.com/go-openapi/jsonreference@v0.0.0-20160704190145-13c6e3589ad9
github.com/go-openapi/spec@v0.0.0-20160808142527-6aced65f8501
github.com/go-openapi/swag@v0.0.0-20160704191624-1d0bd113de87
github.com/go-playground/assert/v2@v2.2.0
github.com/go-playground/locales@v0.14.1
github.com/go-playground/universal-translator@v0.18.1
github.com/go-playground/validator/v10@v10.14.0
github.com/goccy/go-json@v0.10.2
github.com/golang/protobuf@v0.0.0-20161109072736-4bd1920723d7
github.com/golang/protobuf@v1.5.4
github.com/google/cel-go@v0.18.1
github.com/google/gnostic-models@v0.6.8
github.com/google/wire@v0.5.0
github.com/googleapis/gnostic@v0.0.0-20170729233727-0c5108395e2d
github.com/gophercloud/gophercloud@v0.1.0
github.com/grpc-ecosystem/grpc-gateway/v2@v2.16.0
github.com/jackc/pgproto3/v2@v2.3.3
github.com/jackc/pgx/v5@v5.5.4
github.com/jackc/puddle/v2@v2.2.1
github.com/josephspurrier/goversioninfo@v1.4.0
github.com/json-iterator/go@v1.1.8
github.com/klauspost/compress@v1.12.2
github.com/klauspost/cpuid/v2@v2.2.4
github.com/leodido/go-urn@v1.2.4
github.com/lib/pq@v1.10.0
github.com/lib/pq@v1.10.1
github.com/mailru/easyjson@v0.0.0-20160728113105-d5b7844b561a
github.com/mattn/go-isatty@v0.0.19
github.com/matttproud/golang_protobuf_extensions/v2@v2.0.0
github.com/moby/sys/user@v0.1.0
github.com/mrunalp/fileutils@v0.5.1
github.com/mxk/go-flowrate@v0.0.0-20140419014527-cca7078d478f
github.com/onsi/ginkgo/v2@v2.14.0
github.com/onsi/ginkgo@v1.14.0
github.com/onsi/gomega@v1.30.0
github.com/open-policy-agent/opa@v0.43.1
github.com/opencontainers/runc@v1.1.12
github.com/opencontainers/runtime-spec@v1.0.3-0.20220909204839-494a5a6aca78
github.com/opencontainers/runtime-spec@v1.1.0
github.com/pelletier/go-toml/v2@v2.0.8
github.com/prometheus/client_golang@v1.18.0
github.com/prometheus/client_model@v0.5.0
github.com/prometheus/common@v0.45.0
github.com/prometheus/procfs@v0.12.0
github.com/sergi/go-diff@v1.3.2-0.20230802210424-5b0b94c5c0d3
github.com/skeema/knownhosts@v1.2.2
github.com/stretchr/objx@v0.5.2
github.com/stretchr/testify@v1.9.0
github.com/tektoncd/pipeline@v0.54.0
github.com/twitchyliquid64/golang-asm@v0.15.1
github.com/ugorji/go/codec@v1.2.11
github.com/vektah/gqlparser/v2@v2.4.6
go.opentelemetry.io/contrib/instrumentation/google.golang.org/grpc/otelgrpc@v0.45.0
go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp@v0.45.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc@v1.19.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp@v1.19.0
go.opentelemetry.io/otel/exporters/otlp/otlptrace@v1.19.0
go.opentelemetry.io/proto/otlp@v1.0.0
go.uber.org/goleak@v1.3.0
go.uber.org/multierr@v1.11.0
go.uber.org/zap@v1.16.0
gocloud.dev@v0.23.0
golang.org/x/arch@v0.0.0-20210923205945-b76863e36670
golang.org/x/arch@v0.3.0
golang.org/x/crypto@v0.0.0-20190211182817-74369b46fc67
golang.org/x/crypto@v0.0.0-20200220183623-bac4c82f6975
golang.org/x/crypto@v0.0.0-20201221181555-eec23a3978ad
golang.org/x/crypto@v0.0.0-20210506145944-38f3c27a63bf
golang.org/x/crypto@v0.17.0
golang.org/x/crypto@v0.19.0
golang.org/x/crypto@v0.21.0
golang.org/x/mod@v0.14.0
golang.org/x/net@v0.0.0-20170114055629-f2499483f923
golang.org/x/net@v0.0.0-20210420210106-798c2154c571
golang.org/x/net@v0.0.0-20210505214959-0714010a04ed
golang.org/x/net@v0.22.0
golang.org/x/net@v0.23.0
golang.org/x/oauth2@v0.0.0-20210413134643-5e61552d6c78
golang.org/x/oauth2@v0.0.0-20210427180440-81ed05c6b58c
golang.org/x/sys@v0.0.0-20170830134202-bb24a47a89ea
golang.org/x/sys@v0.0.0-20190209173611-3b5209105503
golang.org/x/sys@v0.0.0-20210223095934-7937bea0104d
golang.org/x/sys@v0.0.0-20210412220455-f1c623a9e750
golang.org/x/sys@v0.0.0-20210420205809-ac73e9fd8988
golang.org/x/sys@v0.0.0-20210503080704-8803ae5d1324
golang.org/x/sys@v0.0.0-20210503173754-0981d6026fa6
golang.org/x/sys@v0.0.0-20220704084225-05e143d24a9e
golang.org/x/sys@v0.15.0
golang.org/x/sys@v0.17.0
golang.org/x/sys@v0.18.0
golang.org/x/term@v0.15.0
golang.org/x/term@v0.17.0
golang.org/x/term@v0.18.0
golang.org/x/text@v0.0.0-20160726164857-2910a502d2bf
golang.org/x/tools@v0.0.0-20181011042414-1f849cf54d09
golang.org/x/tools@v0.0.0-20200616133436-c1934b75d054
golang.org/x/tools@v0.16.1
google.golang.org/api@v0.45.0
google.golang.org/api@v0.46.0
google.golang.org/genproto@v0.0.0-20210413151531-c14fb6ef47c3
google.golang.org/genproto@v0.0.0-20210420162539-3c870d7478d2
google.golang.org/genproto@v0.0.0-20210423144448-3a41ef94ed2b
google.golang.org/genproto@v0.0.0-20210429181445-86c259c2b4ab
google.golang.org/genproto@v0.0.0-20210506142907-4a47615972c2
google.golang.org/protobuf@v1.33.0
k8s.io/api@v0.17.8
k8s.io/api@v0.23.6
k8s.io/api@v0.25.4
k8s.io/api@v0.26.2
k8s.io/api@v0.26.5
k8s.io/api@v0.27.1
k8s.io/api@v0.29.0
k8s.io/apiextensions-apiserver@v0.29.0
k8s.io/apimachinery@v0.17.8
k8s.io/apimachinery@v0.23.6
k8s.io/apimachinery@v0.25.0
k8s.io/apimachinery@v0.25.4
k8s.io/apimachinery@v0.26.2
k8s.io/apimachinery@v0.27.1
k8s.io/apimachinery@v0.27.3
k8s.io/apimachinery@v0.29.0
k8s.io/client-go@v0.17.8
k8s.io/client-go@v0.23.6
k8s.io/client-go@v0.25.4
k8s.io/client-go@v0.26.2
k8s.io/client-go@v0.26.5
k8s.io/client-go@v0.27.1
k8s.io/client-go@v0.29.0
k8s.io/code-generator@v0.19.7
k8s.io/code-generator@v0.25.4
k8s.io/code-generator@v0.26.5
k8s.io/code-generator@v0.29.0
k8s.io/component-base@v0.29.0
k8s.io/gengo@v0.0.0-20190128074634-0689ccc1d7d6
k8s.io/gengo@v0.0.0-20200428234225-8167cfdcfc14
k8s.io/gengo@v0.0.0-20230829151522-9cce18d56c01
k8s.io/klog/v2@v2.110.1
k8s.io/klog@v0.0.0-20181102134211-b9b56d5dfc92
k8s.io/klog@v0.3.0
k8s.io/kube-openapi@v0.0.0-20200410145947-bcb3869e6f29
k8s.io/kube-openapi@v0.0.0-20200805222855-6aeccd4b50c6
k8s.io/kube-openapi@v0.0.0-20230308215209-15aac26d736a
k8s.io/kube-openapi@v0.0.0-20231010175941-2dd684a91f00
k8s.io/utils@v0.0.0-20191114184206-e782cd3c129f
k8s.io/utils@v0.0.0-20230209194617-a36077c30491
k8s.io/utils@v0.0.0-20230726121419-3b25d923346b
knative.dev/pkg@v0.0.0-20231023150739-56bfe0dd9626
nhooyr.io/websocket@v1.8.7
sigs.k8s.io/controller-runtime@v0.17.0
sigs.k8s.io/structured-merge-diff/v2@v2.0.1
sigs.k8s.io/structured-merge-diff/v4@v4.0.1
sigs.k8s.io/structured-merge-diff/v4@v4.4.1
sigs.k8s.io/yaml@v1.4.0
tags.cncf.io/container-device-interface/specs-go@v0.6.0
tags.cncf.io/container-device-interface@v0.6.2
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
