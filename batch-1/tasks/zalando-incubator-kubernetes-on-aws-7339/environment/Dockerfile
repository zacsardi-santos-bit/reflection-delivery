FROM golang:1.22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/zalando-incubator/kubernetes-on-aws.git && \
    git fetch --depth 30 origin 9df452cd4ea06cbe2ba3ac6cb5511bfd1e5c9dec && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN mkdir -p /workspace/cluster-test

RUN cat > /workspace/cluster-test/go.mod << 'EOF'
module cluster-config-test

go 1.22
EOF

RUN cat > /workspace/cluster-test/cluster_test.go << 'EOF'
package clustertest

import (
	"os"
	"strings"
	"testing"
)

func TestCronJobTimeZoneFeatureGateRemoved(t *testing.T) {
	content, err := os.ReadFile("/workspace/repo/cluster/config-defaults.yaml")
	if err != nil {
		t.Fatalf("Failed to read cluster/config-defaults.yaml: %v", err)
	}
	if strings.Contains(string(content), "cronjob_time_zone_enabled") {
		t.Fatal("cronjob_time_zone_enabled should be removed from cluster/config-defaults.yaml as CronJobTimeZone feature gate is stable in k8s v1.29")
	}
}

func TestKubernetesV129AMIConfigured(t *testing.T) {
	content, err := os.ReadFile("/workspace/repo/cluster/config-defaults.yaml")
	if err != nil {
		t.Fatalf("Failed to read cluster/config-defaults.yaml: %v", err)
	}
	if !strings.Contains(string(content), "kuberuntu_image_v1_29") {
		t.Fatal("kuberuntu_image_v1_29 AMI entries should be present in cluster/config-defaults.yaml for k8s v1.29 upgrade")
	}
}

func TestMasterNodePoolUsesV129(t *testing.T) {
	content, err := os.ReadFile("/workspace/repo/cluster/node-pools/master-default/stack.yaml")
	if err != nil {
		t.Fatalf("Failed to read master-default/stack.yaml: %v", err)
	}
	if !strings.Contains(string(content), "kuberuntu_image_v1_29") {
		t.Fatal("master-default/stack.yaml should reference kuberuntu_image_v1_29 for k8s v1.29 upgrade")
	}
}
EOF

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
