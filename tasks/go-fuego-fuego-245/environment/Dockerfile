FROM golang:1.23

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/go-fuego/fuego.git && \
    git fetch --depth 30 origin a5ce269924823cf53e4ea299d12ad77d3f3f80be && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

ENV GONOSUMCHECK=*
ENV GONOSUMDB=*
ENV GOWORK=off

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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./... 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
github.com/davecgh/go-spew@v1.1.1
github.com/gabriel-vasile/mimetype@v1.4.8
github.com/getkin/kin-openapi@v0.128.0
github.com/go-fuego/fuego@v0.17.0
github.com/go-openapi/jsonpointer@v0.21.0
github.com/go-openapi/swag@v0.23.0
github.com/go-playground/assert/v2@v2.2.0
github.com/go-playground/locales@v0.14.1
github.com/go-playground/universal-translator@v0.18.1
github.com/go-playground/validator/v10@v10.23.0
github.com/go-test/deep@v1.0.8
github.com/golang-jwt/jwt/v5@v5.2.1
github.com/gorilla/schema@v1.4.1
github.com/invopop/yaml@v0.3.1
github.com/josharian/intern@v1.0.0
github.com/kr/pretty@v0.3.1
github.com/kr/text@v0.2.0
github.com/leodido/go-urn@v1.4.0
github.com/mailru/easyjson@v0.9.0
github.com/mohae/deepcopy@v0.0.0-20170929034955-c48cc78d4826
github.com/perimeterx/marshmallow@v1.1.5
github.com/pmezard/go-difflib@v1.0.0
github.com/rogpeppe/go-internal@v1.12.0
github.com/stretchr/testify@v1.10.0
github.com/thejerf/slogassert@v0.3.4
github.com/ugorji/go/codec@v1.2.12
golang.org/x/crypto@v0.32.0
golang.org/x/net@v0.33.0
golang.org/x/net@v0.34.0
golang.org/x/sys@v0.29.0
golang.org/x/text@v0.21.0
gopkg.in/check.v1@v0.0.0-20161208181325-20d25e280405
gopkg.in/check.v1@v1.0.0-20201130134442-10cb98267c6c
gopkg.in/yaml.v3@v3.0.1
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
