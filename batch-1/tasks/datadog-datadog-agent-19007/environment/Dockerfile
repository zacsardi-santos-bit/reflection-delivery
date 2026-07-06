FROM golang:1.20

RUN apt-get update && apt-get install -y git curl ca-certificates patch build-essential python3 && rm -rf /var/lib/apt/lists/*

RUN mv /usr/bin/git /usr/bin/git.real && echo IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgc3lzLG9zLHN1YnByb2Nlc3MsdGVtcGZpbGUKZGVmIHN0cmlwX2JpbihjKToKICAgIGxpbmVzPWMuc3BsaXQoY2hyKDEwKSk7b3V0PVtdO2k9MAogICAgd2hpbGUgaTxsZW4obGluZXMpOgogICAgICAgIGlmIGxpbmVzW2ldLnN0YXJ0c3dpdGgoImRpZmYgLS1naXQgIik6CiAgICAgICAgICAgIGJsb2NrPVtsaW5lc1tpXV07aj1pKzEKICAgICAgICAgICAgd2hpbGUgajxsZW4obGluZXMpIGFuZCBub3QgbGluZXNbal0uc3RhcnRzd2l0aCgiZGlmZiAtLWdpdCAiKToKICAgICAgICAgICAgICAgIGJsb2NrLmFwcGVuZChsaW5lc1tqXSk7ais9MQogICAgICAgICAgICBpZiBub3QgYW55KGwuc3RhcnRzd2l0aCgiQmluYXJ5IGZpbGVzICIpIGZvciBsIGluIGJsb2NrKToKICAgICAgICAgICAgICAgIG91dCs9YmxvY2sKICAgICAgICAgICAgaT1qCiAgICAgICAgZWxzZToKICAgICAgICAgICAgb3V0LmFwcGVuZChsaW5lc1tpXSk7aSs9MQogICAgcmV0dXJuIGNocigxMCkuam9pbihvdXQpCmFyZ3M9c3lzLmFyZ3ZbMTpdO3JnPSIvdXNyL2Jpbi9naXQucmVhbCIKaWYgYXJncyBhbmQgYXJnc1swXT09ImFwcGx5IiBhbmQgYXJncyBhbmQgb3MucGF0aC5pc2ZpbGUoYXJnc1stMV0pOgogICAgYz1vcGVuKGFyZ3NbLTFdLCJyIixlcnJvcnM9InJlcGxhY2UiKS5yZWFkKCkKICAgIHQ9dGVtcGZpbGUuTmFtZWRUZW1wb3JhcnlGaWxlKG1vZGU9InciLHN1ZmZpeD0iLnBhdGNoIixkZWxldGU9RmFsc2UpCiAgICB0LndyaXRlKHN0cmlwX2JpbihjKSk7dC5jbG9zZSgpCiAgICByPXN1YnByb2Nlc3MucnVuKFtyZ10rYXJnc1s6LTFdK1t0Lm5hbWVdKQogICAgb3MudW5saW5rKHQubmFtZSk7c3lzLmV4aXQoci5yZXR1cm5jb2RlKQpzeXMuZXhpdChzdWJwcm9jZXNzLnJ1bihbcmddK2FyZ3MpLnJldHVybmNvZGUpCg== | base64 -d > /usr/bin/git && chmod +x /usr/bin/git

WORKDIR /workspace

RUN /usr/bin/git.real init /workspace/repo && cd /workspace/repo && \
    /usr/bin/git.real remote add origin https://github.com/DataDog/datadog-agent.git && \
    /usr/bin/git.real fetch --depth 30 origin 69a4e15aac147070a02f48dcfe6678c1fdbb58fb && \
    /usr/bin/git.real checkout --detach FETCH_HEAD && \
    /usr/bin/git.real submodule update --init --recursive --depth 1 2>/dev/null || true && \
    /usr/bin/git.real remote remove origin && \
    /usr/bin/git.real tag -l | xargs -r /usr/bin/git.real tag -d && \
    /usr/bin/git.real for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 /usr/bin/git.real update-ref -d && \
    /usr/bin/git.real reflog expire --expire=now --all && \
    /usr/bin/git.real gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download 2>&1 | tail -5 || true

RUN go build -tags "kubeapiserver test" ./pkg/workloadmeta/... 2>&1 | tail -5 || true

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
