FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/kubeflow/pipelines.git && \
    git fetch --depth 30 origin 43c306b5d0f550d869cf46573b16e80656803c8f && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download 2>&1 | tail -5 || true

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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./backend/src/v2/expression 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
cloud.google.com/go/compute/metadata@v0.2.3
cloud.google.com/go/compute@v1.23.0
cloud.google.com/go/iam@v1.1.2
cloud.google.com/go/storage@v1.30.1
cloud.google.com/go@v0.110.8
github.com/Azure/go-autorest/autorest/adal@v0.9.18
github.com/Azure/go-autorest/autorest/adal@v0.9.20
github.com/Azure/go-autorest/autorest/mocks@v0.4.2
github.com/Azure/go-autorest/autorest@v0.11.27
github.com/Masterminds/goutils@v1.1.1
github.com/Masterminds/semver/v3@v3.1.1
github.com/Masterminds/sprig/v3@v3.2.2
github.com/antlr/antlr4/runtime/Go/antlr@v1.4.10
github.com/antonmedv/expr@v1.9.0
github.com/argoproj/pkg@v0.11.0
github.com/asaskevich/govalidator@v0.0.0-20200907205600-7a23bdc65eef
github.com/aws/aws-sdk-go@v1.45.25
github.com/beorn7/perks@v1.0.1
github.com/buger/jsonparser@v1.1.1
github.com/cespare/xxhash/v2@v2.2.0
github.com/colinmarc/hdfs@v1.1.4-0.20180805212432-9746310a4d31
github.com/davecgh/go-spew@v1.1.1
github.com/doublerebel/bellows@v0.0.0-20160303004610-f177d92a03d3
github.com/elazarl/goproxy/ext@v0.0.0-20190711103511-473e67f1d7d2
github.com/elazarl/goproxy@v0.0.0-20230808193330-2592e75ae04a
github.com/emicklei/go-restful/v3@v3.10.2
github.com/evanphx/json-patch@v5.6.0+incompatible
github.com/flowstack/go-jsonschema@v0.1.1
github.com/fsnotify/fsnotify@v1.6.0
github.com/go-logr/logr@v1.2.3
github.com/go-logr/logr@v1.2.4
github.com/go-logr/zapr@v1.2.3
github.com/go-openapi/analysis@v0.20.1
github.com/go-openapi/jsonpointer@v0.19.6
github.com/go-openapi/jsonreference@v0.20.2
github.com/go-openapi/loads@v0.21.0
github.com/go-openapi/spec@v0.20.4
github.com/go-openapi/swag@v0.22.3
github.com/go-stack/stack@v1.8.0
github.com/go-task/slim-sprig@v0.0.0-20230315185526-52ccab3ef572
github.com/gogo/protobuf@v1.3.2
github.com/golang-jwt/jwt/v4@v4.2.0
github.com/golang/glog@v1.1.0
github.com/golang/groupcache@v0.0.0-20210331224755-41bb18bfe9da
github.com/golang/protobuf@v1.5.3
github.com/google/cel-go@v0.12.6
github.com/google/gnostic@v0.6.9
github.com/google/go-cmp@v0.5.8
github.com/google/go-cmp@v0.5.9
github.com/google/go-cmp@v0.6.0
github.com/google/gofuzz@v1.2.0
github.com/google/martian/v3@v3.3.2
github.com/google/pprof@v0.0.0-20210720184732-4bb14d4b1be1
github.com/google/s2a-go@v0.1.7
github.com/google/uuid@v1.3.1
github.com/google/wire@v0.4.0
github.com/googleapis/enterprise-certificate-proxy@v0.3.1
github.com/googleapis/gax-go/v2@v2.12.0
github.com/gorilla/websocket@v1.5.0
github.com/hashicorp/go-uuid@v1.0.3
github.com/hashicorp/hcl@v1.0.0
github.com/huandu/xstrings@v1.3.2
github.com/imdario/mergo@v0.3.13
github.com/imdario/mergo@v0.3.6
github.com/jackc/pgpassfile@v1.0.0
github.com/jackc/pgservicefile@v0.0.0-20221227161230-091c0ba34f0a
github.com/jcmturner/gofork@v1.0.0
github.com/jinzhu/now@v1.1.5
github.com/jmespath/go-jmespath@v0.4.0
github.com/josharian/intern@v1.0.0
github.com/json-iterator/go@v1.1.12
github.com/klauspost/compress@v1.16.5
github.com/klauspost/cpuid/v2@v2.0.4
github.com/klauspost/cpuid/v2@v2.0.9
github.com/klauspost/cpuid@v1.3.1
github.com/klauspost/pgzip@v1.2.5
github.com/kr/pretty@v0.3.1
github.com/lann/builder@v0.0.0-20180802200727-47ae307949d0
github.com/lann/ps@v0.0.0-20150810152359-62de8c46ede0
github.com/lib/pq@v1.10.6
github.com/magiconair/properties@v1.8.5
github.com/mailru/easyjson@v0.7.7
github.com/mattn/go-sqlite3@v1.14.19
github.com/matttproud/golang_protobuf_extensions@v1.0.4
github.com/minio/md5-simd@v1.1.0
github.com/minio/sha256-simd@v1.0.0
github.com/mitchellh/copystructure@v1.2.0
github.com/mitchellh/go-homedir@v1.1.0
github.com/mitchellh/mapstructure@v1.5.0
github.com/mitchellh/reflectwalk@v1.0.2
github.com/moby/spdystream@v0.2.0
github.com/modern-go/concurrent@v0.0.0-20180306012644-bacd9c7ef1dd
github.com/modern-go/reflect2@v1.0.2
github.com/munnerz/goautoneg@v0.0.0-20191010083416-a7dc8b61c822
github.com/oklog/ulid@v1.3.1
github.com/oliveagle/jsonpath@v0.0.0-20180606110733-2e52cf6e6852
github.com/onsi/ginkgo/v2@v2.1.6
github.com/onsi/ginkgo/v2@v2.11.0
github.com/onsi/ginkgo/v2@v2.3.0
github.com/onsi/ginkgo/v2@v2.4.0
github.com/onsi/gomega@v1.20.1
github.com/onsi/gomega@v1.21.1
github.com/onsi/gomega@v1.22.1
github.com/onsi/gomega@v1.23.0
github.com/onsi/gomega@v1.27.10
github.com/opentracing/opentracing-go@v1.2.0
github.com/pelletier/go-toml@v1.9.5
github.com/pmezard/go-difflib@v1.0.0
github.com/prometheus/client_golang@v1.14.0
github.com/prometheus/common@v0.42.0
github.com/prometheus/procfs@v0.9.0
github.com/robfig/cron/v3@v3.0.1
github.com/rogpeppe/go-charset@v0.0.0-20180617210344-2471d30d28b4
github.com/rogpeppe/go-internal@v1.11.0
github.com/rogpeppe/go-internal@v1.9.0
github.com/shopspring/decimal@v1.2.0
github.com/sirupsen/logrus@v1.9.3
github.com/spf13/afero@v1.9.2
github.com/spf13/cast@v1.4.1
github.com/spf13/jwalterweatherman@v1.1.0
github.com/spf13/pflag@v1.0.5
github.com/stoewer/go-strcase@v1.2.0
github.com/stretchr/testify@v1.8.4
github.com/subosito/gotenv@v1.2.0
github.com/valyala/bytebufferpool@v1.0.0
github.com/valyala/fasttemplate@v1.2.1
go.mongodb.org/mongo-driver@v1.7.5
go.opencensus.io@v0.24.0
go.uber.org/goleak@v1.2.1
go.uber.org/multierr@v1.10.0
go.uber.org/zap@v1.26.0
golang.org/x/crypto@v0.0.0-20211215153901-e495a2d5b3d3
golang.org/x/crypto@v0.0.0-20220315160706-3147a52a75dd
golang.org/x/crypto@v0.1.0
golang.org/x/crypto@v0.14.0
golang.org/x/mod@v0.12.0
golang.org/x/mod@v0.6.0
golang.org/x/net@v0.0.0-20220425223048-2871e0cb64e4
golang.org/x/net@v0.1.0
golang.org/x/net@v0.17.0
golang.org/x/net@v0.7.0
golang.org/x/net@v0.8.0
golang.org/x/oauth2@v0.13.0
golang.org/x/sync@v0.4.0
golang.org/x/sys@v0.0.0-20220422013727-9388b58f7150
golang.org/x/sys@v0.0.0-20220715151400-c0bba94af5f8
golang.org/x/sys@v0.0.0-20220908164124-27713097b956
golang.org/x/sys@v0.1.0
golang.org/x/sys@v0.13.0
golang.org/x/sys@v0.6.0
golang.org/x/term@v0.1.0
golang.org/x/term@v0.13.0
golang.org/x/term@v0.6.0
golang.org/x/text@v0.13.0
golang.org/x/text@v0.4.0
golang.org/x/text@v0.8.0
golang.org/x/time@v0.3.0
golang.org/x/tools@v0.13.0
golang.org/x/tools@v0.2.0
golang.org/x/xerrors@v0.0.0-20220907171357-04be3eba64a2
gomodules.xyz/jsonpatch/v2@v2.4.0
google.golang.org/api@v0.147.0
google.golang.org/appengine@v1.6.7
google.golang.org/genproto/googleapis/api@v0.0.0-20231002182017-d307bd883b97
google.golang.org/genproto/googleapis/rpc@v0.0.0-20231009173412-8bfb1ae86b6c
google.golang.org/genproto@v0.0.0-20220107163113-42d7afdf6368
google.golang.org/genproto@v0.0.0-20231002182017-d307bd883b97
google.golang.org/grpc@v1.58.3
google.golang.org/protobuf@v1.28.0
google.golang.org/protobuf@v1.28.1
google.golang.org/protobuf@v1.31.0
gopkg.in/inf.v0@v0.9.1
gopkg.in/ini.v1@v1.66.3
gopkg.in/jcmturner/aescts.v1@v1.0.1
gopkg.in/jcmturner/dnsutils.v1@v1.0.1
gopkg.in/jcmturner/goidentity.v2@v2.0.0
gopkg.in/jcmturner/gokrb5.v5@v5.3.0
gopkg.in/jcmturner/rpc.v0@v0.0.2
gopkg.in/yaml.v2@v2.4.0
gopkg.in/yaml.v3@v3.0.0
k8s.io/api@v0.25.9
k8s.io/api@v0.27.2
k8s.io/apiextensions-apiserver@v0.27.2
k8s.io/apimachinery@v0.26.5
k8s.io/apimachinery@v0.27.3
k8s.io/client-go@v0.25.9
k8s.io/client-go@v0.27.2
k8s.io/code-generator@v0.25.9
k8s.io/code-generator@v0.27.2
k8s.io/component-base@v0.27.2
k8s.io/gengo@v0.0.0-20221011193443-fad74ee6edd9
k8s.io/klog/v2@v2.100.1
k8s.io/klog/v2@v2.70.1
k8s.io/klog/v2@v2.80.1
k8s.io/kube-openapi@v0.0.0-20220803162953-67bda5d908f1
k8s.io/kube-openapi@v0.0.0-20221012153701-172d655c2280
k8s.io/kube-openapi@v0.0.0-20230515203736-54b630e78af5
k8s.io/kubernetes@v1.13.0
k8s.io/utils@v0.0.0-20220728103510-ee6ede2d64ed
k8s.io/utils@v0.0.0-20221107191617-1a15be271d1d
k8s.io/utils@v0.0.0-20230505201702-9f6742963106
sigs.k8s.io/json@v0.0.0-20220713155537-f223a00ba0e2
sigs.k8s.io/json@v0.0.0-20221116044647-bc3834ca7abd
sigs.k8s.io/structured-merge-diff/v4@v4.2.3
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
