FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential qemu-utils \
    && rm -rf /var/lib/apt/lists/*

ENV GO111MODULE=off
ENV GOPATH=/go

RUN mkdir -p /go/src/github.com/Mirantis

RUN git init /go/src/github.com/Mirantis/virtlet && cd /go/src/github.com/Mirantis/virtlet && \
    git remote add origin https://github.com/Mirantis/virtlet.git && \
    git fetch --depth 30 origin f90c636504b0b5a41a6b6dd2b336e729be43d936 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /go/src/github.com/Mirantis/virtlet

RUN rm -rf vendor

RUN git clone https://github.com/golang/glog.git /go/src/github.com/golang/glog \
    && cd /go/src/github.com/golang/glog \
    && git checkout 23def4e6c14b4da8ac2ed8007337bc5eb5007998

RUN git clone https://github.com/davecgh/go-spew.git /go/src/github.com/davecgh/go-spew \
    && cd /go/src/github.com/davecgh/go-spew \
    && git checkout 5215b55f46b2b919f50a1df0eaa5886afe4e3b3d

RUN git clone https://github.com/jonboulle/clockwork.git /go/src/github.com/jonboulle/clockwork \
    && cd /go/src/github.com/jonboulle/clockwork \
    && git checkout bcac9884e7502bb2b474c0339d889cb981a2f27f

RUN git clone https://github.com/nu7hatch/gouuid.git /go/src/github.com/nu7hatch/gouuid \
    && cd /go/src/github.com/nu7hatch/gouuid \
    && git checkout 179d4d0c4d8d407a32af483c2354df1d2c91e6c3

RUN git clone https://gopkg.in/yaml.v2 /go/src/gopkg.in/yaml.v2 \
    && cd /go/src/gopkg.in/yaml.v2 \
    && git checkout eb3733d160e74a9c7e442f435eb3bea458e1d19f

RUN git clone https://github.com/docker/distribution.git /go/src/github.com/docker/distribution \
    && cd /go/src/github.com/docker/distribution \
    && git checkout cd27f179f2c10c5d300e6d09025b538c475b0d51

RUN git clone https://github.com/opencontainers/go-digest.git /go/src/github.com/opencontainers/go-digest

RUN git clone https://github.com/gogo/protobuf.git /go/src/github.com/gogo/protobuf \
    && cd /go/src/github.com/gogo/protobuf \
    && git checkout c0656edd0d9eab7c66d1eb0c568f9039345796f7

RUN git clone https://github.com/google/gofuzz.git /go/src/github.com/google/gofuzz \
    && cd /go/src/github.com/google/gofuzz \
    && git checkout 44d81051d367757e1c7c6a5a86423ece9afcf63c

RUN git clone https://github.com/kubernetes/apimachinery.git /go/src/k8s.io/apimachinery \
    && cd /go/src/k8s.io/apimachinery \
    && git checkout 917740426ad66ff818da4809990480bcc0786a77

RUN git clone https://github.com/kubernetes/api.git /go/src/k8s.io/api \
    && cd /go/src/k8s.io/api \
    && git checkout 4fe9229aaa9d704f8a2a21cdcd50de2bbb6e1b57

RUN git clone https://github.com/kubernetes/client-go.git /go/src/k8s.io/client-go \
    && cd /go/src/k8s.io/client-go \
    && git checkout 4cbb4d746a6a36cf99d6cd4f1b69a6907f49318a

RUN git clone https://github.com/kubernetes/apiextensions-apiserver.git /go/src/k8s.io/apiextension-server \
    && cd /go/src/k8s.io/apiextension-server \
    && git checkout fcd622fe88a4a6efcb5aea9e94ee87324ac1b036

RUN ln -s /go/src/k8s.io/apiextension-server /go/src/k8s.io/apiextensions-apiserver \
    && find /go/src/k8s.io/apiextension-server -name "*.go" -exec sed -i 's|// import "k8s.io/apiextension-server/|// |g' {} \;

RUN git clone https://github.com/json-iterator/go.git /go/src/github.com/json-iterator/go

RUN git clone https://github.com/modern-go/concurrent.git /go/src/github.com/modern-go/concurrent

RUN git clone https://github.com/modern-go/reflect2.git /go/src/github.com/modern-go/reflect2

RUN git clone https://github.com/emicklei/go-restful.git /go/src/github.com/emicklei/go-restful \
    && cd /go/src/github.com/emicklei/go-restful \
    && git checkout ff4f55a206334ef123e4f79bbf348980da81ca46

RUN git clone https://github.com/emicklei/go-restful-swagger12.git /go/src/github.com/emicklei/go-restful-swagger12 \
    && cd /go/src/github.com/emicklei/go-restful-swagger12 \
    && git checkout dcef7f55730566d41eae5db10e7d6981829720f6

RUN git clone https://github.com/go-openapi/analysis.git /go/src/github.com/go-openapi/analysis \
    && cd /go/src/github.com/go-openapi/analysis \
    && git checkout b44dc874b601d9e4e2f6e19140e794ba24bead3b

RUN git clone https://github.com/go-openapi/jsonpointer.git /go/src/github.com/go-openapi/jsonpointer \
    && cd /go/src/github.com/go-openapi/jsonpointer \
    && git checkout 46af16f9f7b149af66e5d1bd010e3574dc06de98

RUN git clone https://github.com/go-openapi/jsonreference.git /go/src/github.com/go-openapi/jsonreference \
    && cd /go/src/github.com/go-openapi/jsonreference \
    && git checkout 13c6e3589ad90f49bd3e3bbe2c2cb3d7a4142272

RUN git clone https://github.com/go-openapi/loads.git /go/src/github.com/go-openapi/loads \
    && cd /go/src/github.com/go-openapi/loads \
    && git checkout 18441dfa706d924a39a030ee2c3b1d8d81917b38

RUN git clone https://github.com/go-openapi/spec.git /go/src/github.com/go-openapi/spec \
    && cd /go/src/github.com/go-openapi/spec \
    && git checkout 6aced65f8501fe1217321abf0749d354824ba2ff

RUN git clone https://github.com/go-openapi/swag.git /go/src/github.com/go-openapi/swag \
    && cd /go/src/github.com/go-openapi/swag \
    && git checkout 1d0bd113de87027671077d3c71eb3ac5d7dbba72

RUN git clone https://github.com/ugorji/go.git /go/src/github.com/ugorji/go \
    && cd /go/src/github.com/ugorji/go \
    && git checkout ded73eae5db7e7a0ef6f55aace87a2873c5d2b74

RUN git clone https://github.com/juju/ratelimit.git /go/src/github.com/juju/ratelimit \
    && cd /go/src/github.com/juju/ratelimit \
    && git checkout 5b9ff866471762aa2ab2dced63c9fb6f53921342

RUN git clone https://github.com/golang/protobuf.git /go/src/github.com/golang/protobuf \
    && cd /go/src/github.com/golang/protobuf \
    && git checkout 4bd1920723d7b7c925de087aa32e2187708897f7

RUN git clone https://gopkg.in/inf.v0 /go/src/gopkg.in/inf.v0 \
    && cd /go/src/gopkg.in/inf.v0 \
    && git checkout 3887ee99ecf07df5b447e9b00d9c0b2adaa9f3e4

RUN git clone https://github.com/ghodss/yaml.git /go/src/github.com/ghodss/yaml \
    && cd /go/src/github.com/ghodss/yaml \
    && git checkout 73d445a93680fa1a78ae23a5839bad48f32ba1ee

RUN git clone https://github.com/mailru/easyjson.git /go/src/github.com/mailru/easyjson \
    && cd /go/src/github.com/mailru/easyjson \
    && git checkout d5b7844b561a7bc640052f1b935f7b800330d7e0

RUN git clone https://github.com/PuerkitoBio/purell.git /go/src/github.com/PuerkitoBio/purell \
    && cd /go/src/github.com/PuerkitoBio/purell \
    && git checkout 8a290539e2e8629dbc4e6bad948158f790ec31f4

RUN git clone https://github.com/PuerkitoBio/urlesc.git /go/src/github.com/PuerkitoBio/urlesc \
    && cd /go/src/github.com/PuerkitoBio/urlesc \
    && git checkout 5bd2802263f21d8788851d5305584c82a5c75d7e

RUN git clone https://github.com/golang/net.git /go/src/golang.org/x/net

RUN git clone https://github.com/golang/text.git /go/src/golang.org/x/text

RUN go build ./pkg/image/... ./pkg/imagetranslation/... 2>&1 | tail -30 || true

RUN mkdir -p /workspace && ln -s /go/src/github.com/Mirantis/virtlet /workspace/repo


# --- offline dep-baking: warm Go module cache at build (network available here) ---
RUN cd /workspace/repo && \
    for d in $(find . -name go.mod -not -path '*/vendor/*' -exec dirname {} \; | sort -u); do \
      ( cd "$d" && echo "warming $d" && \
        (go mod download all 2>/dev/null || go mod download 2>/dev/null || true) && \
        (go test -count=1 -run '^$' ./... 2>/dev/null || true) ) ; \
    done; true
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
