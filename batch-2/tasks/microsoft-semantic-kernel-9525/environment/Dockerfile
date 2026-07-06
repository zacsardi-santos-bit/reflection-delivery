FROM mcr.microsoft.com/dotnet/sdk:8.0

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/microsoft/semantic-kernel.git && \
    git fetch --depth 30 origin ca4badff8ece9483054b5a4a5d8eea6ef13a8da2 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN echo "IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgeG1sLmV0cmVlLkVsZW1lbnRUcmVlIGFzIEVULCBzeXMKCmRlZiBjb252ZXJ0KHBhdGgpOgogICAgdHJ5OgogICAgICAgIHJvb3QgPSBFVC5wYXJzZShwYXRoKS5nZXRyb290KCkKICAgIGV4Y2VwdCBFeGNlcHRpb246CiAgICAgICAgcmV0dXJuCiAgICBucyA9ICJodHRwOi8vbWljcm9zb2Z0LmNvbS9zY2hlbWFzL1Zpc3VhbFN0dWRpby9UZWFtVGVzdC8yMDEwIgogICAgZGVmIHRhZyhuKTogcmV0dXJuICJ7IiArIG5zICsgIn0iICsgbgogICAgdGVzdF9tYXAgPSB7fQogICAgZGVmcyA9IHJvb3QuZmluZCh0YWcoIlRlc3REZWZpbml0aW9ucyIpKQogICAgaWYgZGVmcyBpcyBub3QgTm9uZToKICAgICAgICBmb3IgdXQgaW4gZGVmcy5maW5kYWxsKHRhZygiVW5pdFRlc3QiKSk6CiAgICAgICAgICAgIHRpZCA9IHV0LmdldCgiaWQiLCAiIikKICAgICAgICAgICAgbSA9IHV0LmZpbmQodGFnKCJUZXN0TWV0aG9kIikpCiAgICAgICAgICAgIGlmIG0gaXMgbm90IE5vbmU6CiAgICAgICAgICAgICAgICB0ZXN0X21hcFt0aWRdID0gKG0uZ2V0KCJjbGFzc05hbWUiLCAiIiksIG0uZ2V0KCJuYW1lIiwgIiIpKQogICAgdHMgPSBFVC5FbGVtZW50KCJ0ZXN0c3VpdGUiKQogICAgcmVzdWx0cyA9IHJvb3QuZmluZCh0YWcoIlJlc3VsdHMiKSkKICAgIGlmIHJlc3VsdHMgaXMgbm90IE5vbmU6CiAgICAgICAgZm9yIHIgaW4gcmVzdWx0cy5maW5kYWxsKHRhZygiVW5pdFRlc3RSZXN1bHQiKSk6CiAgICAgICAgICAgIG91dGNvbWUgPSByLmdldCgib3V0Y29tZSIsICIiKQogICAgICAgICAgICB0aWQgPSByLmdldCgidGVzdElkIiwgIiIpCiAgICAgICAgICAgIHRuYW1lID0gci5nZXQoInRlc3ROYW1lIiwgInVua25vd24iKQogICAgICAgICAgICBpZiB0aWQgaW4gdGVzdF9tYXA6CiAgICAgICAgICAgICAgICBjbmFtZSwgbW5hbWUgPSB0ZXN0X21hcFt0aWRdCiAgICAgICAgICAgIGVsc2U6CiAgICAgICAgICAgICAgICBwYXJ0cyA9IHRuYW1lLnJzcGxpdCgiLiIsIDEpCiAgICAgICAgICAgICAgICBjbmFtZSA9IHBhcnRzWzBdIGlmIGxlbihwYXJ0cykgPiAxIGVsc2UgIiIKICAgICAgICAgICAgICAgIG1uYW1lID0gcGFydHNbLTFdCiAgICAgICAgICAgIHRjID0gRVQuU3ViRWxlbWVudCh0cywgInRlc3RjYXNlIikKICAgICAgICAgICAgdGMuc2V0KCJjbGFzc25hbWUiLCBjbmFtZSkKICAgICAgICAgICAgdGMuc2V0KCJuYW1lIiwgbW5hbWUpCiAgICAgICAgICAgIGlmIG91dGNvbWUgIT0gIlBhc3NlZCI6CiAgICAgICAgICAgICAgICBFVC5TdWJFbGVtZW50KHRjLCAiZmFpbHVyZSIpLnNldCgibWVzc2FnZSIsIG91dGNvbWUpCiAgICBwcmludCgiPD94bWwgdmVyc2lvbj1cIjEuMFwiPz4iKQogICAgcHJpbnQoRVQudG9zdHJpbmcodHMsIGVuY29kaW5nPSJ1bmljb2RlIikpCgppZiBsZW4oc3lzLmFyZ3YpID4gMToKICAgIGNvbnZlcnQoc3lzLmFyZ3ZbMV0pCg==" | base64 -d > /usr/local/bin/trx2junit && chmod +x /usr/local/bin/trx2junit

RUN dotnet restore dotnet/src/Experimental/Process.Utilities.UnitTests/Process.Utilities.UnitTests.csproj

RUN dotnet restore dotnet/src/Experimental/Process.Runtime.Dapr/Process.Runtime.Dapr.csproj

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
