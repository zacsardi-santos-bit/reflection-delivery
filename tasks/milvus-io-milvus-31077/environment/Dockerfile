FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential protobuf-compiler \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/milvus-io/milvus.git && \
    git fetch --depth 30 origin d0eeea4b443f5353421de69a22d83dc1fa4dee23 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go install github.com/golang/protobuf/protoc-gen-go@v1.3.2 2>&1 | tail -5

RUN mkdir -p cmake_build/thirdparty && \
    git clone https://github.com/milvus-io/milvus-proto.git cmake_build/thirdparty/milvus-proto && \
    cd cmake_build/thirdparty/milvus-proto && \
    git reset --hard a922b16f2a46

RUN cd /workspace/repo/internal/proto && \
    mkdir -p etcdpb indexcgopb internalpb rootcoordpb segcorepb proxypb indexpb datapb querypb planpb && \
    mkdir -p /workspace/repo/cmd/tools/migration/legacy/legacypb && \
    API=/workspace/repo/cmake_build/thirdparty/milvus-proto/proto && \
    protoc --proto_path=${API} --proto_path=. --go_out=plugins=grpc,paths=source_relative:./etcdpb etcd_meta.proto && \
    protoc --proto_path=${API} --proto_path=. --go_out=plugins=grpc,paths=source_relative:./indexcgopb index_cgo_msg.proto && \
    protoc --proto_path=${API} --proto_path=. --go_out=plugins=grpc,paths=source_relative:./internalpb internal.proto && \
    protoc --proto_path=${API} --proto_path=. --go_out=plugins=grpc,paths=source_relative:./rootcoordpb root_coord.proto && \
    protoc --proto_path=${API} --proto_path=. --go_out=plugins=grpc,paths=source_relative:./proxypb proxy.proto && \
    protoc --proto_path=${API} --proto_path=. --go_out=plugins=grpc,paths=source_relative:./indexpb index_coord.proto && \
    protoc --proto_path=${API} --proto_path=. --go_out=plugins=grpc,paths=source_relative:./datapb data_coord.proto && \
    protoc --proto_path=${API} --proto_path=. --go_out=plugins=grpc,paths=source_relative:./querypb query_coord.proto && \
    protoc --proto_path=${API} --proto_path=. --go_out=plugins=grpc,paths=source_relative:./planpb plan.proto && \
    protoc --proto_path=${API} --proto_path=. --go_out=plugins=grpc,paths=source_relative:./segcorepb segcore.proto

RUN go mod download 2>&1 | tail -5

RUN go build ./internal/distributed/datacoord/client/... 2>&1 | tail -20 || true

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
