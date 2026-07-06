FROM ubuntu:18.04
ENV DEBIAN_FRONTEND=noninteractive
ENV MPLBACKEND=Agg
SHELL ["/bin/bash", "-c"]

RUN apt-get update && apt-get install -y \
    build-essential git curl ca-certificates patch wget unzip zip \
    python3 python3-dev python3-pip python3-venv libpython3-dev \
    python3-scipy python3-numpy python3-matplotlib python3-lxml \
    openjdk-11-jdk \
    libblas-dev liblapack-dev \
    libeigen3-dev \
    && rm -rf /var/lib/apt/lists/*

RUN pip3 install --trusted-host pypi-mirror.modal.local -q virtualenv pytest

RUN wget -q https://github.com/bazelbuild/bazel/releases/download/2.2.0/bazel-2.2.0-installer-linux-x86_64.sh && \
    chmod +x bazel-2.2.0-installer-linux-x86_64.sh && \
    ./bazel-2.2.0-installer-linux-x86_64.sh --bin=/usr/local/bin/ && \
    rm bazel-2.2.0-installer-linux-x86_64.sh

RUN mkdir -p /tmp/eigen_repo && \
    cp -r /usr/include/eigen3/Eigen /tmp/eigen_repo/ && \
    printf '%s\n' "cc_library(" "    name = 'eigen'," "    srcs = []," "    includes = ['.']," "    hdrs = glob(['Eigen/**'])," "    visibility = ['//visibility:public']," ")" > /tmp/eigen_repo/BUILD && \
    touch /tmp/eigen_repo/WORKSPACE

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/bark-simulator/bark.git && \
    git fetch --depth 30 origin 03d90549967f6d221c66efef6e71c45e3c2b54cf && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN virtualenv -p python3 python/venv --system-site-packages && \
    mkdir -p python/venv/include && \
    ln -sfn /usr/include/python3.6m python/venv/include/python3.6m && \
    mkdir -p python/venv/lib && \
    (find /usr/lib -name "libpython3.6m.so" 2>/dev/null | head -1 | xargs -r -I{} ln -sf {} python/venv/lib/libpython3.6m.so) || true

RUN bazel build //python:bark.so --override_repository=com_github_eigen_eigen=/tmp/eigen_repo

WORKDIR /workspace

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH

# dv01-cpp-go-toolchain: install Go and put it on login PATH (client-req toolchain check for cpp)
RUN (command -v go >/dev/null 2>&1) || ( \
      ARCH=$(dpkg --print-architecture 2>/dev/null || echo amd64); \
      case "$ARCH" in arm64) GOA=arm64;; *) GOA=amd64;; esac; \
      cd /tmp && (curl -fsSL -o go.tgz https://go.dev/dl/go1.22.5.linux-$GOA.tar.gz || wget -qO go.tgz https://go.dev/dl/go1.22.5.linux-$GOA.tar.gz) && \
      rm -rf /usr/local/go && tar -C /usr/local -xzf go.tgz && rm -f go.tgz ) || true
RUN printf 'export PATH="/usr/local/go/bin:%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
