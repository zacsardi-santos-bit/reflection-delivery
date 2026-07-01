FROM golang:1.22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/zalando-incubator/kubernetes-on-aws.git && \
    git fetch --depth 30 origin 3af3c809e6021dcd3d115995f5198b2bb82dfa8a && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN mkdir -p /workspace/configtest

RUN printf 'module configtest\n\ngo 1.22\n' > /workspace/configtest/go.mod

RUN printf 'package configtest\n\nimport (\n\t"os"\n\t"strings"\n\t"testing"\n)\n\nfunc TestKubeletImageGCConfig(t *testing.T) {\n\tdata, err := os.ReadFile("/workspace/repo/cluster/config-defaults.yaml")\n\tif err != nil {\n\t\tt.Fatalf("Failed to read config-defaults.yaml: %%v", err)\n\t}\n\tcontents := string(data)\n\tif !strings.Contains(contents, "kubelet_image_gc_high_threshold") {\n\t\tt.Fatal("kubelet_image_gc_high_threshold not found in cluster/config-defaults.yaml")\n\t}\n\tif !strings.Contains(contents, "kubelet_image_gc_low_threshold") {\n\t\tt.Fatal("kubelet_image_gc_low_threshold not found in cluster/config-defaults.yaml")\n\t}\n}\n\nfunc TestCronJobTimeZoneRemoved(t *testing.T) {\n\tdata, err := os.ReadFile("/workspace/repo/cluster/config-defaults.yaml")\n\tif err != nil {\n\t\tt.Fatalf("Failed to read config-defaults.yaml: %%v", err)\n\t}\n\tif strings.Contains(string(data), "cronjob_time_zone_enabled") {\n\t\tt.Fatal("cronjob_time_zone_enabled should be removed from cluster/config-defaults.yaml")\n\t}\n}\n' > /workspace/configtest/config_test.go

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
