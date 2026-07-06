FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/lestrrat-go/jwx.git && \
    git fetch --depth 30 origin e75b7c8dcbf8e56ecfbeb17b8b3ffaa81a96914f && \
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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./jws 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
github.com/BurntSushi/toml@v1.3.2
github.com/cpuguy83/go-md2man/v2@v2.0.3
github.com/davecgh/go-spew@v1.1.1
github.com/fatih/color@v1.16.0
github.com/goccy/go-json@v0.10.2
github.com/goccy/go-yaml@v1.11.2
github.com/google/go-cmp@v0.5.9
github.com/lestrrat-go/blackmagic@v1.0.2
github.com/lestrrat-go/httpcc@v1.0.1
github.com/lestrrat-go/jwx/v2@v2.0.18
github.com/mattn/go-isatty@v0.0.20
github.com/pmezard/go-difflib@v1.0.0
github.com/urfave/cli/v2@v2.26.0
github.com/xrash/smetrics@v0.0.0-20231213231151-1d8dd44e695e
golang.org/x/crypto@v0.13.0
golang.org/x/crypto@v0.16.0
golang.org/x/crypto@v0.17.0
golang.org/x/mod@v0.12.0
golang.org/x/mod@v0.14.0
golang.org/x/net@v0.10.0
golang.org/x/net@v0.15.0
golang.org/x/net@v0.19.0
golang.org/x/sync@v0.3.0
golang.org/x/sync@v0.5.0
golang.org/x/sys@v0.12.0
golang.org/x/sys@v0.14.0
golang.org/x/sys@v0.15.0
golang.org/x/sys@v0.8.0
golang.org/x/term@v0.12.0
golang.org/x/term@v0.15.0
golang.org/x/term@v0.8.0
golang.org/x/text@v0.13.0
golang.org/x/text@v0.14.0
golang.org/x/text@v0.9.0
golang.org/x/tools@v0.13.0
golang.org/x/tools@v0.16.1
golang.org/x/xerrors@v0.0.0-20231012003039-104605ab7028
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
