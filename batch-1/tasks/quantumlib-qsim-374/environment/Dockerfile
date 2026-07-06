FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

SHELL ["/bin/bash", "-c"]

RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    ca-certificates \
    libeigen3-dev \
    python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/quantumlib/qsim.git && \
    git fetch --depth 30 origin 0514a02997ceeb27656ad329ec1eb4e98e664f73 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN git submodule update --init tests/googletest

RUN cd tests/googletest/googletest && \
    mkdir -p make && cd make && \
    cmake .. -DCMAKE_BUILD_TYPE=Release 2>&1 | tail -5 && \
    make -j$(nproc) 2>&1 | tail -5

RUN echo '#!/usr/bin/env python3' > /usr/local/bin/run_mps_gtest && \
    echo 'import subprocess, sys, json, os' >> /usr/local/bin/run_mps_gtest && \
    echo 'r = subprocess.run(["/workspace/repo/tests/mps_statespace_test.x"] + sys.argv[1:])' >> /usr/local/bin/run_mps_gtest && \
    echo 'f = "/workspace/test-results/output.json"' >> /usr/local/bin/run_mps_gtest && \
    echo 'if os.path.exists(f):' >> /usr/local/bin/run_mps_gtest && \
    echo '    d = json.load(open(f)); json.dump({"testsuites": d.get("testsuites", [])}, open(f, "w"), indent=2)' >> /usr/local/bin/run_mps_gtest && \
    echo 'sys.exit(r.returncode)' >> /usr/local/bin/run_mps_gtest && \
    chmod +x /usr/local/bin/run_mps_gtest

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
