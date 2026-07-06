FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential gcc g++ \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/uber/aresdb.git && \
    git fetch --depth 30 origin b1ddf343b0759f40d2c2dc4da5d9d202e2c59a8d && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN mkdir -p lib && gcc -shared -fPIC -I . -o lib/libmem.so cgoutils/memory/malloc.c

RUN printf '#include "query/time_series_aggregate.h"\n\
extern "C" {\n\
CGoCallResHandle InitIndexVector(uint32_t *iv, uint32_t s, int l, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle HashLookup(InputVector in, RecordID *out, uint32_t *iv, int ivl, uint32_t *bc, uint32_t sc, CuckooHashIndex hi, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle UnaryTransform(InputVector in, OutputVector out, uint32_t *iv, int ivl, uint32_t *bc, uint32_t sc, enum UnaryFunctorType ft, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle UnaryFilter(InputVector in, uint32_t *iv, uint8_t *pv, int ivl, RecordID **rv, int nft, uint32_t *bc, uint32_t sc, enum UnaryFunctorType ft, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle BinaryTransform(InputVector lhs, InputVector rhs, OutputVector out, uint32_t *iv, int ivl, uint32_t *bc, uint32_t sc, enum BinaryFunctorType ft, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle BinaryFilter(InputVector lhs, InputVector rhs, uint32_t *iv, uint8_t *pv, int ivl, RecordID **rv, int nft, uint32_t *bc, uint32_t sc, enum BinaryFunctorType ft, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle Sort(DimensionVector keys, int length, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle Reduce(DimensionVector ik, uint8_t *iv, DimensionVector ok, uint8_t *ov, int vb, int l, enum AggregateFunction af, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle HashReduce(DimensionVector ik, uint8_t *iv, DimensionVector ok, uint8_t *ov, int vb, int l, enum AggregateFunction af, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle Expand(DimensionVector ik, DimensionVector ok, uint32_t *bc, uint32_t *iv, int ivl, int ool, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle HyperLogLog(DimensionVector pd, DimensionVector cd, uint32_t *pv, uint32_t *cv, int prs, int cbs, bool ilb, uint8_t **hvp, size_t *hvsp, uint16_t **hdrc, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle GeoBatchIntersects(GeoShapeBatch gsb, InputVector pts, uint32_t *iv, int ivl, uint32_t sc, RecordID **rv, int nft, uint32_t *op, bool io, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle WriteGeoShapeDim(int stw, DimensionOutputVector dov, int ivlbg, uint32_t *op, void *cs, int d) { CGoCallResHandle r = {0,0}; return r; }\n\
CGoCallResHandle BootstrapDevice() { CGoCallResHandle r = {0,0}; return r; }\n\
}\n' > /tmp/algorithm_stub.cpp

RUN g++ -shared -fPIC -fpermissive -I /workspace/repo -o /workspace/repo/lib/libalgorithm.so /tmp/algorithm_stub.cpp -L /workspace/repo/lib -lmem

ENV LD_LIBRARY_PATH=/workspace/repo/lib:${LD_LIBRARY_PATH}
ENV GO111MODULE=on
ENV CGO_ENABLED=1
ENV GONOSUMDB=*
ENV GOFLAGS=-mod=mod

RUN go mod download 2>&1 | tail -20
RUN go mod tidy 2>&1 | tail -20

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
