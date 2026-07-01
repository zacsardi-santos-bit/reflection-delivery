FROM gcc:13

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    cmake ninja-build git ca-certificates curl python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/dmlc/xgboost.git && \
    git fetch --depth 30 origin 01c471155627f9d81c230d0a16a7655c9fdd16c4 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN cd /workspace/repo && git submodule update --init dmlc-core

RUN mkdir -p /workspace/test-results

RUN cd /workspace/repo && mkdir -p build && cd build && \
    cmake .. -GNinja -DGOOGLE_TEST=ON -DUSE_DMLC_GTEST=ON -DUSE_OPENMP=ON -DCMAKE_BUILD_TYPE=Release && \
    ninja -v

RUN echo "IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgc3VicHJvY2Vzcywgc3lzLCBqc29uLCBvcywgc2h1dGlsCgphcmdzID0gc3lzLmFyZ3ZbMTpdCmd0ZXN0X291dCA9ICIvd29ya3NwYWNlL3Rlc3QtcmVzdWx0cy9vdXRwdXQuanNvbiIKZmlsdGVyZWRfYXJncyA9IFtdCmZvciBhcmcgaW4gYXJnczoKICAgIGlmIGFyZy5zdGFydHN3aXRoKCItLWd0ZXN0X291dHB1dD1qc29uOiIpOgogICAgICAgIGd0ZXN0X291dCA9IGFyZ1tsZW4oIi0tZ3Rlc3Rfb3V0cHV0PWpzb246Iik6XQogICAgZWxzZToKICAgICAgICBmaWx0ZXJlZF9hcmdzLmFwcGVuZChhcmcpCgpjbWQgPSBbIi93b3Jrc3BhY2UvcmVwby9idWlsZC90ZXN0eGdib29zdCJdICsgZmlsdGVyZWRfYXJncyArIFsiLS1ndGVzdF9vdXRwdXQ9anNvbjovdG1wL2d0ZXN0X3Jhdy5qc29uIl0KcmVzdWx0ID0gc3VicHJvY2Vzcy5ydW4oY21kKQoKaWYgb3MucGF0aC5leGlzdHMoIi90bXAvZ3Rlc3RfcmF3Lmpzb24iKToKICAgIHRyeToKICAgICAgICB3aXRoIG9wZW4oIi90bXAvZ3Rlc3RfcmF3Lmpzb24iKSBhcyBmOgogICAgICAgICAgICBkID0ganNvbi5sb2FkKGYpCiAgICAgICAgdHMgPSBkLnBvcCgidGVzdHN1aXRlcyIsIFtdKQogICAgICAgIG91dCA9IHsidGVzdHN1aXRlcyI6IHRzfQogICAgICAgIG91dC51cGRhdGUoZCkKICAgICAgICBvcy5tYWtlZGlycyhvcy5wYXRoLmRpcm5hbWUoZ3Rlc3Rfb3V0KSBvciAiLiIsIGV4aXN0X29rPVRydWUpCiAgICAgICAgd2l0aCBvcGVuKGd0ZXN0X291dCwgInciKSBhcyBmOgogICAgICAgICAgICBqc29uLmR1bXAob3V0LCBmLCBpbmRlbnQ9MikKICAgIGV4Y2VwdCBFeGNlcHRpb246CiAgICAgICAgc2h1dGlsLmNvcHkoIi90bXAvZ3Rlc3RfcmF3Lmpzb24iLCBndGVzdF9vdXQpCgpzeXMuZXhpdChyZXN1bHQucmV0dXJuY29kZSkK" | base64 -d > /workspace/run_gtest_wrapper.py && chmod +x /workspace/run_gtest_wrapper.py

WORKDIR /workspace/repo

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
