FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential ninja-build \
    && rm -rf /var/lib/apt/lists/*

RUN pip install pytest

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/mesonbuild/meson.git && \
    git fetch --depth 30 origin 3778a34979c1f705eda044960cc1f89233409f88 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN mkdir -p /tmp/testdata/subprojects/sub && \
    echo "project('My Project', version : '1.0')" > /tmp/testdata/meson.build && \
    echo "" >> /tmp/testdata/meson.build && \
    echo "subproject('sub')" >> /tmp/testdata/meson.build && \
    echo "subproject('sub2', required : false)" >> /tmp/testdata/meson.build && \
    echo "" >> /tmp/testdata/meson.build && \
    echo "summary('Configuration', {'Some boolean': false," >> /tmp/testdata/meson.build && \
    echo "                          'Another boolean': true," >> /tmp/testdata/meson.build && \
    echo "                          'Some string': 'Hello World'," >> /tmp/testdata/meson.build && \
    echo "                          'A list': ['string', 1, true]," >> /tmp/testdata/meson.build && \
    echo "                          })" >> /tmp/testdata/meson.build && \
    echo "summary('Configuration', 'A number', 1)" >> /tmp/testdata/meson.build && \
    echo "summary('Configuration', 'yes', true, bool_yn : true)" >> /tmp/testdata/meson.build && \
    echo "summary('Configuration', 'no', false, bool_yn : true)" >> /tmp/testdata/meson.build && \
    echo "project('Some Subproject', version : '2.0')" > /tmp/testdata/subprojects/sub/meson.build && \
    echo "" >> /tmp/testdata/subprojects/sub/meson.build && \
    echo "summary('string', 'bar')" >> /tmp/testdata/subprojects/sub/meson.build && \
    echo "summary({'integer': 1, 'boolean': true})" >> /tmp/testdata/subprojects/sub/meson.build

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
