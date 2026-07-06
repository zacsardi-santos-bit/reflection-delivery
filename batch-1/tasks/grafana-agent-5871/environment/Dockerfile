FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential wget \
    && rm -rf /var/lib/apt/lists/*

RUN wget -q https://get.helm.sh/helm-v3.10.3-linux-amd64.tar.gz -O /tmp/helm.tar.gz \
    && tar -xzf /tmp/helm.tar.gz -C /tmp \
    && mv /tmp/linux-amd64/helm /usr/local/bin/helm \
    && rm -rf /tmp/helm.tar.gz /tmp/linux-amd64

RUN mkdir -p /workspace/helmtest && \
    printf 'package helmtest_test\n\nimport (\n\t"os"\n\t"testing"\n)\n\nfunc TestHelmSidecarsValues(t *testing.T) {\n\tdata, err := os.ReadFile("/tmp/helm_result")\n\tif err != nil {\n\t\tt.Fatalf("helm result not found: %%v", err)\n\t}\n\tresult := string(data)\n\tif result != "PASS\\n" {\n\t\tt.Fatalf("helm sidecars test failed: %%s", result)\n\t}\n}\n' > /workspace/helmtest/helm_test.go && \
    printf 'module helmtest\n\ngo 1.21\n' > /workspace/helmtest/go.mod

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/grafana/agent.git && \
    git fetch --depth 30 origin 50d16208d1c2f30851212b97ef1fe6e106d499e8 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

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
