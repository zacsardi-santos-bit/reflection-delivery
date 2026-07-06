FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/openziti/ziti.git && \
    git fetch --depth 30 origin 159820547d1b7cbdfcfb708844aeee7f33ba8ecf && \
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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./tests 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
github.com/go-logr/logr@v1.4.1
github.com/go-openapi/analysis@v0.22.1
github.com/go-openapi/jsonpointer@v0.20.2
github.com/go-openapi/jsonreference@v0.20.4
github.com/go-openapi/loads@v0.21.5
github.com/go-openapi/spec@v0.20.13
github.com/go-openapi/strfmt@v0.22.0
github.com/go-openapi/validate@v0.22.6
github.com/go-resty/resty/v2@v2.11.0
github.com/golang-jwt/jwt/v5@v5.2.0
github.com/google/gofuzz@v1.2.0
github.com/gorilla/schema@v1.2.1
github.com/gorilla/securecookie@v1.1.2
github.com/jeremija/gosubmit@v0.2.7
github.com/muhlemmer/httpforwarded@v0.1.0
github.com/openziti/sdk-golang@v0.22.1
github.com/openziti/sdk-golang@v0.22.5
github.com/openziti/sdk-golang@v0.22.6
github.com/shirou/gopsutil/v3@v3.23.12
github.com/tklauser/go-sysconf@v0.3.13
github.com/tklauser/numcpus@v0.7.0
github.com/zitadel/oidc/v2@v2.12.0
golang.org/x/exp@v0.0.0-20240103183307-be819d1f06fc
golang.org/x/mod@v0.14.0
golang.org/x/tools@v0.16.0
google.golang.org/appengine@v1.6.8
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
