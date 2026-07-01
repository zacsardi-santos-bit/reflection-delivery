FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/MontFerret/ferret.git && \
    git fetch --depth 30 origin 446ce3ead5812fe105726bae16196fb7ce4a7185 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

ENV GO111MODULE=off
ENV GOPATH=/go

RUN mkdir -p /go/src/github.com/MontFerret && \
    mv /workspace/repo /go/src/github.com/MontFerret/ferret && \
    ln -s /go/src/github.com/MontFerret/ferret /workspace/repo

RUN curl -L -o /usr/local/bin/dep https://github.com/golang/dep/releases/download/v0.5.4/dep-linux-amd64 && \
    chmod +x /usr/local/bin/dep

WORKDIR /go/src/github.com/MontFerret/ferret

RUN dep ensure

RUN GLS=/go/src/github.com/MontFerret/ferret/vendor/github.com/jtolds/gls && \
    rm -f ${GLS}/*.go ${GLS}/*.s && \
    printf 'package gls\n\nimport (\n\t"runtime"\n\t"strconv"\n\t"strings"\n)\n\nfunc curGoroutineID() uint64 {\n\tvar buf [64]byte\n\tn := runtime.Stack(buf[:], false)\n\ts := string(buf[:n])\n\tfields := strings.Fields(s)\n\tif len(fields) < 2 {\n\t\treturn 0\n\t}\n\tid, _ := strconv.ParseUint(fields[1], 10, 64)\n\treturn id\n}\n' > ${GLS}/gid.go && \
    printf 'package gls\n\nimport "sync"\n\ntype Values map[interface{}]interface{}\n\ntype ContextManager struct {\n\tmu    sync.Mutex\n\tstore map[uint64]map[interface{}]interface{}\n}\n\nfunc NewContextManager() *ContextManager {\n\treturn &ContextManager{\n\t\tstore: make(map[uint64]map[interface{}]interface{}),\n\t}\n}\n\nfunc (c *ContextManager) SetValues(values Values, callback func()) {\n\tid := curGoroutineID()\n\tc.mu.Lock()\n\tprev := c.store[id]\n\tmerged := make(map[interface{}]interface{})\n\tfor k, v := range prev {\n\t\tmerged[k] = v\n\t}\n\tfor k, v := range values {\n\t\tmerged[k] = v\n\t}\n\tc.store[id] = merged\n\tc.mu.Unlock()\n\tdefer func() {\n\t\tc.mu.Lock()\n\t\tif prev == nil {\n\t\t\tdelete(c.store, id)\n\t\t} else {\n\t\t\tc.store[id] = prev\n\t\t}\n\t\tc.mu.Unlock()\n\t}()\n\tcallback()\n}\n\nfunc (c *ContextManager) GetValue(key interface{}) (interface{}, bool) {\n\tid := curGoroutineID()\n\tc.mu.Lock()\n\tdefer c.mu.Unlock()\n\tif m, ok := c.store[id]; ok {\n\t\tv, ok := m[key]\n\t\treturn v, ok\n\t}\n\treturn nil, false\n}\n\nfunc EnsureGoroutineId(cb func(id uint64)) {\n\tcb(curGoroutineID())\n}\n' > ${GLS}/gls.go

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

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
