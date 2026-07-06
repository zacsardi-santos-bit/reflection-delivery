FROM golang:1.23

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/siderolabs/talos.git && \
    git fetch --depth 30 origin c774835103ad139b44d7e4e13c003e2b13160347 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN printf '//go:build integration\n\npackage integration_test\n\nimport (\n\t"reflect"\n\t"testing"\n\n\t"github.com/siderolabs/talos/internal/integration/base"\n)\n\nfunc TestUserDisksReturnType(t *testing.T) {\n\tmethod, ok := reflect.TypeOf(&base.APISuite{}).MethodByName("UserDisks")\n\tif !ok {\n\t\tt.Fatal("UserDisks method not found")\n\t}\n\tif method.Type.NumOut() != 1 {\n\t\tt.Fatalf("expected UserDisks to return 1 value ([]string), got %%d", method.Type.NumOut())\n\t}\n}\n' > /workspace/repo/internal/integration/userdisks_check_test.go

RUN cd /workspace/repo && \
    git config user.email "test@example.com" && \
    git config user.name "Test" && \
    git add internal/integration/userdisks_check_test.go && \
    git commit -m "Add UserDisks return type check test" && git reset --soft c774835103ad139b44d7e4e13c003e2b13160347 && rm -rf .git/logs .git/refs/original 2>/dev/null

WORKDIR /workspace/repo

RUN go mod download 2>&1 | tail -5

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
