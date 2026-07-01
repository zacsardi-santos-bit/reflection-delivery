FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV QT_QPA_PLATFORM=offscreen
ENV PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install -y \
    git \
    curl \
    ca-certificates \
    patch \
    python3 \
    python3-pip \
    python3-dev \
    build-essential \
    libxcb-xinerama0 \
    libxcb1 \
    libgl1 \
    libglib2.0-0 \
    libdbus-1-3 \
    libxkbcommon-x11-0 \
    libfontconfig1 \
    libfreetype6 \
    libx11-6 \
    libx11-xcb1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/spyder-ide/spyder.git && \
    git fetch --depth 30 origin 9312dfd0887b05bb8fc2baed08f3db50df3b0be9 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --break-system-packages --upgrade pip setuptools wheel 2>&1 | tail -5

RUN pip install --break-system-packages \
    "pyqt5>=5.15,<5.16" \
    "qtpy>=2.4.0" \
    "qdarkstyle>=3.2.0,<3.3.0" \
    "qstylizer>=0.2.2" \
    "qtawesome>=1.3.1,<1.4.0" \
    "qtconsole>=5.6.1,<5.7.0" \
    2>&1 | tail -10

RUN pip install --break-system-packages \
    "spyder-kernels>=3.0.0,<3.2.0" \
    2>&1 | tail -10

RUN pip install --break-system-packages -e ".[test]" --no-deps 2>&1 | tail -10

RUN pip install --break-system-packages \
    "aiohttp>=3.9.3" \
    "asyncssh>=2.14.0,<3.0.0" \
    "atomicwrites>=1.2.0" \
    "chardet>=2.0.0" \
    "cloudpickle>=0.5.0" \
    "cookiecutter>=1.6.0" \
    "diff-match-patch>=20181111" \
    "importlib-metadata>=4.6.0" \
    "intervaltree>=3.0.2" \
    "ipython>=8.13.0,<9.0.0,!=8.17.1" \
    "jedi>=0.17.2,<0.20.0" \
    "jellyfish>=0.7" \
    "jsonschema>=3.2.0" \
    "keyring>=17.0.0" \
    "nbconvert>=4.0" \
    "numpydoc>=0.6.0" \
    "parso>=0.7.0,<0.9.0" \
    "pexpect>=4.4.0" \
    "pickleshare>=0.4" \
    "psutil>=5.3" \
    "pygments>=2.0" \
    "pylint>=3.1,<4" \
    "pyzmq>=24.0.0" \
    "setuptools>=49.6.0" \
    "sphinx>=0.6.6" \
    "superqt>=0.6.2,<1.0.0" \
    "textdistance>=4.2.0" \
    "three-merge>=0.1.1" \
    "watchdog>=0.10.3" \
    "yarl>=1.9.4" \
    2>&1 | tail -10

RUN pip install --break-system-packages \
    "pytest<8.0" \
    "pytest-cov" \
    "pytest-mock" \
    "pytest-order" \
    "pytest-qt" \
    "pytest-timeout" \
    2>&1 | tail -10

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
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
