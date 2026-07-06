FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/cert-manager/cert-manager.git && \
    git fetch --depth 30 origin 8aafddb974beda74b56adcfb2a83ffb5c3a26bf4 && \
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
    cloud.google.com/go/compute/metadata@v0.2.3 \
    cloud.google.com/go/compute@v1.23.0 \
    cloud.google.com/go@v0.110.7 \
    github.com/DATA-DOG/go-sqlmock@v1.5.0 \
    github.com/Microsoft/go-winio@v0.6.1 \
    github.com/Microsoft/hcsshim@v0.10.0-rc.8 \
    github.com/Shopify/logrus-bugsnag@v0.0.0-20171204204709-577dee27f20d \
    github.com/Venafi/vcert/v5@v5.1.1 \
    github.com/armon/go-socks5@v0.0.0-20160902184237-e75332964ef5 \
    github.com/asaskevich/govalidator@v0.0.0-20230301143203-a9d515a09cc2 \
    github.com/aws/aws-sdk-go@v1.45.8 \
    github.com/benbjohnson/clock@v1.3.0 \
    github.com/bshuster-repo/logrus-logstash-hook@v1.0.0 \
    github.com/bugsnag/bugsnag-go@v0.0.0-20141110184014-b1d153021fcd \
    github.com/bugsnag/osext@v0.0.0-20130617224835-0dd3f918b21b \
    github.com/bugsnag/panicwrap@v0.0.0-20151223152923-e2c28503fcd0 \
    github.com/cert-manager/cert-manager@v1.13.0-beta.0.0.20230912132115-a91834eec9b3 \
    github.com/cncf/xds/go@v0.0.0-20230607035331-e9ce68804cb4 \
    github.com/containerd/cgroups@v1.1.0 \
    github.com/containerd/continuity@v0.3.0 \
    github.com/creack/pty@v1.1.18 \
    github.com/davecgh/go-spew@v1.1.2-0.20180830191138-d8f796af33cc \
    github.com/distribution/distribution/v3@v3.0.0-20221208165359-362910506bc2 \
    github.com/docker/cli@v23.0.6+incompatible \
    github.com/docker/docker@v23.0.6+incompatible \
    github.com/docker/docker@v24.0.5+incompatible \
    github.com/docker/go-events@v0.0.0-20190806004212-e31b211e4f1c \
    github.com/docker/libtrust@v0.0.0-20150114040149-fa567046d9b1 \
    github.com/dustin/go-humanize@v1.0.1 \
    github.com/envoyproxy/protoc-gen-validate@v1.0.2 \
    github.com/evanphx/json-patch/v5@v5.7.0 \
    github.com/evanphx/json-patch@v5.7.0+incompatible \
    github.com/fatih/color@v1.15.0 \
    github.com/fatih/structs@v1.1.0 \
    github.com/felixge/httpsnoop@v1.0.3 \
    github.com/foxcpp/go-mockdns@v1.0.0 \
    github.com/frankban/quicktest@v1.14.3 \
    github.com/fsnotify/fsnotify@v1.6.0 \
    github.com/go-openapi/jsonpointer@v0.20.0 \
    github.com/go-openapi/swag@v0.22.4 \
    github.com/go-task/slim-sprig@v0.0.0-20230315185526-52ccab3ef572 \
    github.com/go-test/deep@v1.1.0 \
    github.com/golang-jwt/jwt/v4@v4.5.0 \
    github.com/golang/glog@v1.1.0 \
    github.com/golang/groupcache@v0.0.0-20210331224755-41bb18bfe9da \
    github.com/golang/protobuf@v1.5.2 \
    github.com/gomodule/redigo@v1.8.2 \
    github.com/google/btree@v1.0.1 \
    github.com/google/go-cmp@v0.5.7 \
    github.com/google/pprof@v0.0.0-20210720184732-4bb14d4b1be1 \
    github.com/google/uuid@v1.3.1 \
    github.com/gorilla/handlers@v1.5.1 \
    github.com/grpc-ecosystem/go-grpc-middleware@v1.3.0 \
    github.com/grpc-ecosystem/grpc-gateway/v2@v2.18.0 \
    github.com/hashicorp/go-hclog@v1.5.0 \
    github.com/hashicorp/go-sockaddr@v1.0.5 \
    github.com/hashicorp/golang-lru@v0.5.4 \
    github.com/imdario/mergo@v0.3.16 \
    github.com/jmespath/go-jmespath@v0.4.1-0.20220621161143-b0104c826a24 \
    github.com/jonboulle/clockwork@v0.2.2 \
    github.com/klauspost/compress@v1.16.5 \
    github.com/kr/pretty@v0.3.0 \
    github.com/kr/pretty@v0.3.1 \
    github.com/kr/text@v0.2.0 \
    github.com/mattn/go-colorable@v0.1.13 \
    github.com/mattn/go-isatty@v0.0.17 \
    github.com/miekg/dns@v1.1.56 \
    github.com/moby/sys/mountinfo@v0.6.2 \
    github.com/nxadm/tail@v1.4.8 \
    github.com/onsi/ginkgo/v2@v2.12.0 \
    github.com/onsi/ginkgo@v1.16.5 \
    github.com/onsi/gomega@v1.27.10 \
    github.com/phayes/freeport@v0.0.0-20220201140144-74d24b5ae9f5 \
    github.com/pmezard/go-difflib@v1.0.1-0.20181226105442-5d4384ee4fb2 \
    github.com/prometheus/procfs@v0.11.1 \
    github.com/rogpeppe/go-internal@v1.11.0 \
    github.com/rogpeppe/go-internal@v1.6.1 \
    github.com/soheilhy/cmux@v0.1.5 \
    github.com/sosodev/duration@v1.1.0 \
    github.com/stoewer/go-strcase@v1.3.0 \
    github.com/stretchr/testify@v1.8.4 \
    github.com/tmc/grpc-websocket-proxy@v0.0.0-20220101234140-673ab2c3ae75 \
    github.com/yuin/goldmark@v1.4.13 \
    github.com/yvasiyarov/go-metrics@v0.0.0-20140926110328-57bccd1ccd43 \
    github.com/yvasiyarov/gorelic@v0.0.0-20141212073537-a9bba5b9ab50 \
    github.com/yvasiyarov/newrelic_platform_go@v0.0.0-20140908184405-b21fdbd4370f \
    go.etcd.io/bbolt@v1.3.7 \
    go.etcd.io/etcd/client/v2@v2.305.9 \
    go.etcd.io/etcd/pkg/v3@v3.5.9 \
    go.etcd.io/etcd/raft/v3@v3.5.9 \
    go.etcd.io/etcd/server/v3@v3.5.9 \
    go.opencensus.io@v0.24.0 \
    go.opentelemetry.io/contrib/instrumentation/google.golang.org/grpc/otelgrpc@v0.44.0 \
    go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp@v0.44.0 \
    go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc@v1.18.0 \
    go.opentelemetry.io/otel/exporters/otlp/otlptrace@v1.18.0 \
    go.opentelemetry.io/otel/metric@v1.18.0 \
    go.opentelemetry.io/otel/sdk@v1.18.0 \
    go.opentelemetry.io/otel/trace@v1.18.0 \
    go.opentelemetry.io/otel@v1.18.0 \
    go.opentelemetry.io/proto/otlp@v1.0.0 \
    go.uber.org/goleak@v1.2.1 \
    golang.org/x/crypto@v0.0.0-20210921155107-089bfa567519 \
    golang.org/x/crypto@v0.13.0 \
    golang.org/x/exp@v0.0.0-20230905200255-921286631fa9 \
    golang.org/x/mod@v0.12.0 \
    golang.org/x/mod@v0.6.0-dev.0.20220419223038-86c51ed26bb4 \
    golang.org/x/net@v0.0.0-20210226172049-e18ecbb05110 \
    golang.org/x/net@v0.0.0-20220722155237-a158d28d115b \
    golang.org/x/net@v0.15.0 \
    golang.org/x/oauth2@v0.12.0 \
    golang.org/x/sync@v0.0.0-20220722155255-886fb9371eb4 \
    golang.org/x/sys@v0.0.0-20210615035016-665e8c7367d1 \
    golang.org/x/sys@v0.0.0-20220520151302-bc2c85ada10a \
    golang.org/x/sys@v0.0.0-20220722155257-8c9f86f7a55f \
    golang.org/x/sys@v0.12.0 \
    golang.org/x/term@v0.0.0-20210927222741-03fcf44c2211 \
    golang.org/x/term@v0.12.0 \
    golang.org/x/text@v0.13.0 \
    golang.org/x/text@v0.3.7 \
    golang.org/x/text@v0.3.8 \
    golang.org/x/tools@v0.1.12 \
    golang.org/x/tools@v0.13.0 \
    gomodules.xyz/jsonpatch/v2@v2.4.0 \
    google.golang.org/appengine@v1.6.8 \
    google.golang.org/genproto/googleapis/api@v0.0.0-20230911183012-2d3300fd4832 \
    google.golang.org/genproto/googleapis/rpc@v0.0.0-20230911183012-2d3300fd4832 \
    google.golang.org/genproto@v0.0.0-20230911183012-2d3300fd4832 \
    google.golang.org/grpc@v1.58.0 \
    gopkg.in/check.v1@v1.0.0-20201130134442-10cb98267c6c \
    gopkg.in/ini.v1@v1.67.0 \
    gopkg.in/natefinch/lumberjack.v2@v2.2.1 \
    gopkg.in/tomb.v1@v1.0.0-20141024135613-dd632973f1e7 \
    gotest.tools/v3@v3.4.0 \
    k8s.io/kube-openapi@v0.0.0-20230905202853-d090da108d2f \
    sigs.k8s.io/apiserver-network-proxy/konnectivity-client@v0.1.4 \
    sigs.k8s.io/controller-runtime@v0.16.2 \
    sigs.k8s.io/gateway-api@v0.8.0 \
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
