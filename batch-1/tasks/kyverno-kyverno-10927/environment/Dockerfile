FROM golang:1.22

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/kyverno/kyverno.git && \
    git fetch --depth 30 origin 2140a0239b8ca878f2629bec399ed9a633ac8fc4 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download

RUN mkdir -p /tmp/marshaltest

RUN printf 'package marshaltest\n\nimport (\n\t"encoding/json"\n\t"strings"\n\t"testing"\n\n\tv1 "github.com/kyverno/kyverno/api/kyverno/v1"\n)\n\nfunc TestGlobalContextEntryReferenceNameRequired(t *testing.T) {\n\tref := v1.GlobalContextEntryReference{JMESPath: "items | length(@)"}\n\tdata, err := json.Marshal(ref)\n\tif err != nil {\n\t\tt.Fatal(err)\n\t}\n\tif !strings.Contains(string(data), `"name"`) {\n\t\tt.Errorf("Expected name field in JSON output (name must not be omitempty), got: %%s", data)\n\t}\n}\n' > /tmp/marshaltest/main_test.go

RUN printf 'module marshaltest\n\ngo 1.22\n\nrequire github.com/kyverno/kyverno v0.0.0\n\nreplace github.com/kyverno/kyverno => /workspace/repo\n' > /tmp/marshaltest/go.mod

RUN cd /tmp/marshaltest && go mod tidy 2>&1 | tail -5

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
