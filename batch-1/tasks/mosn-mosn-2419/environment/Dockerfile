FROM golang:1.18

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/mosn/mosn.git && \
    git fetch --depth 30 origin d37fe18cf4860b678c693942e8c809d06de8ed25 && \
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
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./pkg/filter/stream/flowcontrol ./pkg/filter/stream/flowcontrol/data_source 2>/dev/null; true
RUN cat > /tmp/_gobake_coords.txt <<'__GOBAKE__'
github.com/alibaba/sentinel-golang/pkg/datasource/nacos@v0.0.0-20241028104215-b8d94d173765
github.com/alibaba/sentinel-golang@v1.0.4
github.com/aliyun/alibaba-cloud-sdk-go@v1.61.18
github.com/buger/jsonparser@v0.0.0-20181115193947-bf1c66bbce23
github.com/fastly/go-utils@v0.0.0-20180712184237-d95a45783239
github.com/go-errors/errors@v1.0.1
github.com/go-ole/go-ole@v1.2.5
github.com/jehiah/go-strftime@v0.0.0-20171201141054-1d33003b3869
github.com/jmespath/go-jmespath@v0.0.0-20180206201540-c2b33e8439af
github.com/jonboulle/clockwork@v0.2.2
github.com/lestrrat/go-envload@v0.0.0-20180220120943-6ed08b54a570
github.com/lestrrat/go-file-rotatelogs@v0.0.0-20180223000712-d3151e2a480f
github.com/lestrrat/go-strftime@v0.0.0-20180220042222-ba3bf9c1d042
github.com/nacos-group/nacos-sdk-go@v1.0.8
github.com/shirou/gopsutil/v3@v3.21.6
github.com/stretchr/objx@v0.4.0
github.com/stretchr/objx@v0.5.0
github.com/stretchr/testify@v1.7.1
github.com/stretchr/testify@v1.8.0
github.com/tebeka/strftime@v0.1.3
github.com/tklauser/go-sysconf@v0.3.6
github.com/tklauser/numcpus@v0.2.2
github.com/toolkits/concurrent@v0.0.0-20150624120057-a4371d70e3e3
golang.org/x/sys@v0.0.0-20190916202348-b4ddaad3f8a3
golang.org/x/sys@v0.0.0-20210316164454-77fc1eacc6aa
gopkg.in/ini.v1@v1.51.0
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
