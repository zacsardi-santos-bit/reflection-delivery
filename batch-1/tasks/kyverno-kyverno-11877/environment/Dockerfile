FROM golang:1.23

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/kyverno/kyverno.git && \
    git fetch --depth 30 origin 25ac400481ff631dc0e1131b47b376720e3737a7 && \
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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./api/kyverno/v1 ./api/kyverno/v2 ./api/kyverno/v2beta1 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
cel.dev/expr@v0.18.0
github.com/AliyunContainerService/ack-ram-tool/pkg/credentials/provider@v0.14.0
github.com/elazarl/goproxy@v1.2.3
github.com/go-git/go-git/v5@v5.13.1
github.com/google/cel-go@v0.22.0
go.etcd.io/bbolt@v1.3.11
go.etcd.io/etcd/api/v3@v3.5.16
go.etcd.io/etcd/client/pkg/v3@v3.5.16
go.etcd.io/etcd/client/v2@v2.305.16
go.etcd.io/etcd/client/v3@v3.5.16
go.etcd.io/etcd/pkg/v3@v3.5.16
go.etcd.io/etcd/raft/v3@v3.5.16
go.etcd.io/etcd/server/v3@v3.5.16
go.opentelemetry.io/auto/sdk@v1.1.0
golang.org/x/exp@v0.0.0-20250106191152-7588d65b2ba8
golang.org/x/mod@v0.22.0
golang.org/x/oauth2@v0.25.0
golang.org/x/time@v0.7.0
golang.org/x/tools@v0.29.0
k8s.io/api@v0.32.0
k8s.io/apiextensions-apiserver@v0.32.0
k8s.io/apimachinery@v0.32.0
k8s.io/apiserver@v0.32.0
k8s.io/cli-runtime@v0.32.0
k8s.io/client-go@v0.32.0
k8s.io/component-base@v0.32.0
k8s.io/kms@v0.32.0
k8s.io/kube-aggregator@v0.32.0
k8s.io/kube-openapi@v0.0.0-20241212222426-2c72e554b1e7
k8s.io/kubectl@v0.32.0
k8s.io/utils@v0.0.0-20241210054802-24370beab758
sigs.k8s.io/apiserver-network-proxy/konnectivity-client@v0.31.1
sigs.k8s.io/controller-runtime@v0.19.4
sigs.k8s.io/json@v0.0.0-20241014173422-cfa47c3a1cc8
sigs.k8s.io/kubectl-validate@v0.0.5-0.20241223122011-eb064d2f92d5
sigs.k8s.io/release-utils@v0.9.0
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
