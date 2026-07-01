FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential openssl python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/golang-samples && cd /workspace/golang-samples && \
    git remote add origin https://github.com/GoogleCloudPlatform/golang-samples.git && \
    git fetch --depth 30 origin 8dbfe6f77461da49508ab37ccf1204324ab83629 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN ln -s /workspace/golang-samples /workspace/repo

WORKDIR /workspace/golang-samples

RUN go mod download 2>&1 | tail -5 || true

RUN openssl genrsa -out /tmp/ca.key 2048 && \
    openssl req -new -x509 -days 3650 -key /tmp/ca.key -out /tmp/ca.crt -subj "/CN=FakeCA" && \
    openssl genrsa -out /tmp/server.key 2048 && \
    openssl req -new -key /tmp/server.key -out /tmp/server.csr -subj "/CN=us-central1-aiplatform.googleapis.com" && \
    printf "[SAN]\nsubjectAltName=DNS:us-central1-aiplatform.googleapis.com\n" > /tmp/san.ext && \
    openssl x509 -req -days 3650 -in /tmp/server.csr -CA /tmp/ca.crt -CAkey /tmp/ca.key \
      -CAcreateserial -out /tmp/server.crt -extfile /tmp/san.ext -extensions SAN && \
    cp /tmp/ca.crt /usr/local/share/ca-certificates/fake-aiplatform-ca.crt && \
    update-ca-certificates

RUN openssl genrsa -out /tmp/sa-raw.key 2048 && \
    openssl pkcs8 -topk8 -inform PEM -outform PEM -nocrypt -in /tmp/sa-raw.key -out /tmp/sa.key

RUN echo aW1wb3J0IGpzb24Ka2V5ID0gb3BlbignL3RtcC9zYS5rZXknKS5yZWFkKCkKY3JlZHMgPSB7CiAgICAndHlwZSc6ICdzZXJ2aWNlX2FjY291bnQnLAogICAgJ3Byb2plY3RfaWQnOiAndGVzdCcsCiAgICAncHJpdmF0ZV9rZXlfaWQnOiAna2V5MScsCiAgICAncHJpdmF0ZV9rZXknOiBrZXksCiAgICAnY2xpZW50X2VtYWlsJzogJ3Rlc3RAdGVzdC5pYW0uZ3NlcnZpY2VhY2NvdW50LmNvbScsCiAgICAnY2xpZW50X2lkJzogJzEyMzQ1Njc4OScsCiAgICAnYXV0aF91cmknOiAnaHR0cHM6Ly9hY2NvdW50cy5nb29nbGUuY29tL28vb2F1dGgyL2F1dGgnLAogICAgJ3Rva2VuX3VyaSc6ICdodHRwOi8vbG9jYWxob3N0OjgwODAvdG9rZW4nLAogICAgJ2F1dGhfcHJvdmlkZXJfeDUwOV9jZXJ0X3VybCc6ICdodHRwczovL3d3dy5nb29nbGVhcGlzLmNvbS9vYXV0aDIvdjEvY2VydHMnLAogICAgJ2NsaWVudF94NTA5X2NlcnRfdXJsJzogJ2h0dHBzOi8vd3d3Lmdvb2dsZWFwaXMuY29tL3JvYm90L3YxL21ldGFkYXRhL3g1MDkvdGVzdCcKfQpqc29uLmR1bXAoY3JlZHMsIG9wZW4oJy90bXAvZmFrZS1jcmVkcy5qc29uJywgJ3cnKSkK | base64 -d | python3

RUN echo aW1wb3J0IGh0dHAuc2VydmVyLCBqc29uCmNsYXNzIEgoaHR0cC5zZXJ2ZXIuQmFzZUhUVFBSZXF1ZXN0SGFuZGxlcik6CiAgICBkZWYgZG9fUE9TVChzZWxmKToKICAgICAgICBzZWxmLnNlbmRfcmVzcG9uc2UoMjAwKQogICAgICAgIHNlbGYuc2VuZF9oZWFkZXIoIkNvbnRlbnQtVHlwZSIsICJhcHBsaWNhdGlvbi9qc29uIikKICAgICAgICBzZWxmLmVuZF9oZWFkZXJzKCkKICAgICAgICBzZWxmLndmaWxlLndyaXRlKGpzb24uZHVtcHMoeyJhY2Nlc3NfdG9rZW4iOiJmYWtlIiwidG9rZW5fdHlwZSI6IkJlYXJlciIsImV4cGlyZXNfaW4iOjM2MDB9KS5lbmNvZGUoKSkKICAgIGRlZiBsb2dfbWVzc2FnZShzZWxmLCAqYSk6IHBhc3MKaHR0cC5zZXJ2ZXIuSFRUUFNlcnZlcigoIiIsIDgwODApLCBIKS5zZXJ2ZV9mb3JldmVyKCkK | base64 -d > /usr/local/bin/fake-token-server.py

RUN mkdir -p /workspace/fakeserver && \
    echo cGFja2FnZSBtYWluCgppbXBvcnQgKAoJImNvbnRleHQiCgkiY3J5cHRvL3RscyIKCSJsb2ciCgkibmV0IgoKCWFpcGxhdGZvcm1wYiAiY2xvdWQuZ29vZ2xlLmNvbS9nby9haXBsYXRmb3JtL2FwaXYxYmV0YTEvYWlwbGF0Zm9ybXBiIgoJImdvb2dsZS5nb2xhbmcub3JnL2dycGMiCgkiZ29vZ2xlLmdvbGFuZy5vcmcvZ3JwYy9jcmVkZW50aWFscyIKKQoKdHlwZSBzZXJ2ZXIgc3RydWN0IHsKCWFpcGxhdGZvcm1wYi5VbmltcGxlbWVudGVkRXZhbHVhdGlvblNlcnZpY2VTZXJ2ZXIKfQoKZnVuYyAocyAqc2VydmVyKSBFdmFsdWF0ZUluc3RhbmNlcyhfIGNvbnRleHQuQ29udGV4dCwgXyAqYWlwbGF0Zm9ybXBiLkV2YWx1YXRlSW5zdGFuY2VzUmVxdWVzdCkgKCphaXBsYXRmb3JtcGIuRXZhbHVhdGVJbnN0YW5jZXNSZXNwb25zZSwgZXJyb3IpIHsKCXJldHVybiAmYWlwbGF0Zm9ybXBiLkV2YWx1YXRlSW5zdGFuY2VzUmVzcG9uc2V7fSwgbmlsCn0KCmZ1bmMgbWFpbigpIHsKCWNlcnQsIGVyciA6PSB0bHMuTG9hZFg1MDlLZXlQYWlyKCIvdG1wL3NlcnZlci5jcnQiLCAiL3RtcC9zZXJ2ZXIua2V5IikKCWlmIGVyciAhPSBuaWwgewoJCWxvZy5GYXRhbChlcnIpCgl9CgljcmVkcyA6PSBjcmVkZW50aWFscy5OZXdUTFMoJnRscy5Db25maWd7Q2VydGlmaWNhdGVzOiBbXXRscy5DZXJ0aWZpY2F0ZXtjZXJ0fX0pCglsaXMsIGVyciA6PSBuZXQuTGlzdGVuKCJ0Y3AiLCAiOjQ0MyIpCglpZiBlcnIgIT0gbmlsIHsKCQlsb2cuRmF0YWwoZXJyKQoJfQoJcyA6PSBncnBjLk5ld1NlcnZlcihncnBjLkNyZWRzKGNyZWRzKSkKCWFpcGxhdGZvcm1wYi5SZWdpc3RlckV2YWx1YXRpb25TZXJ2aWNlU2VydmVyKHMsICZzZXJ2ZXJ7fSkKCWlmIGVyciA6PSBzLlNlcnZlKGxpcyk7IGVyciAhPSBuaWwgewoJCWxvZy5GYXRhbChlcnIpCgl9Cn0K | base64 -d > /workspace/fakeserver/main.go && \
    echo bW9kdWxlIGZha2VzZXJ2ZXIKCmdvIDEuMjEKCnJlcXVpcmUgKAoJY2xvdWQuZ29vZ2xlLmNvbS9nby9haXBsYXRmb3JtIHYxLjY4LjAKCWdvb2dsZS5nb2xhbmcub3JnL2dycGMgdjEuNjcuMQopCg== | base64 -d > /workspace/fakeserver/go.mod

WORKDIR /workspace/fakeserver

RUN go mod tidy && go build -o /usr/local/bin/fake-aiplatform-server .

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
