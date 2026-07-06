FROM golang:1.21

RUN apt-get update && apt-get install -y     git curl ca-certificates patch build-essential     postgresql     && rm -rf /var/lib/apt/lists/*

RUN find /etc/postgresql -name pg_hba.conf -exec sed -i 's/peer$/trust/; s/scram-sha-256$/trust/; s/md5$/trust/' {} \;

RUN find /etc/postgresql -name postgresql.conf -exec sed -i "s/^#*listen_addresses.*/listen_addresses = 'localhost'/" {} \;

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/xataio/pgroll.git && \
    git fetch --depth 30 origin 7b7caa06776f53370ff08eb3e955d7b7382184a1 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN echo "Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEFwYWNoZS0yLjAKCnBhY2thZ2UgdGVzdHV0aWxzCgppbXBvcnQgKAoJImNvbnRleHQiCgkiZGF0YWJhc2Uvc3FsIgoJImZtdCIKCSJsb2ciCgkibmV0L3VybCIKCSJvcyIKCSJ0ZXN0aW5nIgoKCSJnaXRodWIuY29tL2xpYi9wcSIKCSJnaXRodWIuY29tL3hhdGFpby9wZ3JvbGwvcGtnL3JvbGwiCgkiZ2l0aHViLmNvbS94YXRhaW8vcGdyb2xsL3BrZy9zdGF0ZSIKKQoKdmFyIHRDb25uU3RyIHN0cmluZwoKZnVuYyBTaGFyZWRUZXN0TWFpbihtICp0ZXN0aW5nLk0pIHsKCWN0eCA6PSBjb250ZXh0LkJhY2tncm91bmQoKQoKCWNvbm5TdHIgOj0gb3MuR2V0ZW52KCJQT1NUR1JFU19EU04iKQoJaWYgY29ublN0ciA9PSAiIiB7CgkJY29ublN0ciA9ICJwb3N0Z3JlczovL3Bvc3RncmVzQGxvY2FsaG9zdDo1NDMyL3Bvc3RncmVzP3NzbG1vZGU9ZGlzYWJsZSIKCX0KCgl0Q29ublN0ciA9IGNvbm5TdHIKCglkYiwgZXJyIDo9IHNxbC5PcGVuKCJwb3N0Z3JlcyIsIHRDb25uU3RyKQoJaWYgZXJyICE9IG5pbCB7CgkJbG9nLlByaW50ZigiRmFpbGVkIHRvIG9wZW4gREI6ICV2IiwgZXJyKQoJCW9zLkV4aXQoMSkKCX0KCWRlZmVyIGRiLkNsb3NlKCkKCglpZiBlcnIgOj0gZGIuUGluZ0NvbnRleHQoY3R4KTsgZXJyICE9IG5pbCB7CgkJbG9nLlByaW50ZigiRmFpbGVkIHRvIHBpbmcgREI6ICV2IiwgZXJyKQoJCW9zLkV4aXQoMSkKCX0KCglfLCBfID0gZGIuRXhlY0NvbnRleHQoY3R4LCAiQ1JFQVRFIFJPTEUgcGdyb2xsIikKCglleGl0Q29kZSA6PSBtLlJ1bigpCglvcy5FeGl0KGV4aXRDb2RlKQp9CgpmdW5jIFdpdGhTdGF0ZUFuZENvbm5lY3Rpb25Ub0NvbnRhaW5lcih0ICp0ZXN0aW5nLlQsIGZuIGZ1bmMoKnN0YXRlLlN0YXRlLCAqc3FsLkRCKSkgewoJdC5IZWxwZXIoKQoJY3R4IDo9IGNvbnRleHQuQmFja2dyb3VuZCgpCgoJdERCLCBlcnIgOj0gc3FsLk9wZW4oInBvc3RncmVzIiwgdENvbm5TdHIpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCgl0LkNsZWFudXAoZnVuYygpIHsKCQlpZiBlcnIgOj0gdERCLkNsb3NlKCk7IGVyciAhPSBuaWwgewoJCQl0LkZhdGFsZigiRmFpbGVkIHRvIGNsb3NlIGRhdGFiYXNlIGNvbm5lY3Rpb246ICV2IiwgZXJyKQoJCX0KCX0pCgoJZGJOYW1lIDo9IHJhbmRvbURCTmFtZSgpCgoJXywgZXJyID0gdERCLkV4ZWNDb250ZXh0KGN0eCwgZm10LlNwcmludGYoIkNSRUFURSBEQVRBQkFTRSAlcyIsIHBxLlF1b3RlSWRlbnRpZmllcihkYk5hbWUpKSkKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCXUsIGVyciA6PSB1cmwuUGFyc2UodENvbm5TdHIpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCgl1LlBhdGggPSAiLyIgKyBkYk5hbWUKCWNvbm5TdHIgOj0gdS5TdHJpbmcoKQoKCXN0LCBlcnIgOj0gc3RhdGUuTmV3KGN0eCwgY29ublN0ciwgInBncm9sbCIpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCglkYiwgZXJyIDo9IHNxbC5PcGVuKCJwb3N0Z3JlcyIsIGNvbm5TdHIpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCgl0LkNsZWFudXAoZnVuYygpIHsKCQlpZiBlcnIgOj0gZGIuQ2xvc2UoKTsgZXJyICE9IG5pbCB7CgkJCXQuRmF0YWxmKCJGYWlsZWQgdG8gY2xvc2UgZGF0YWJhc2UgY29ubmVjdGlvbjogJXYiLCBlcnIpCgkJfQoJfSkKCglmbihzdCwgZGIpCn0KCmZ1bmMgV2l0aE1pZ3JhdG9ySW5TY2hlbWFBbmRDb25uZWN0aW9uVG9Db250YWluZXJXaXRoT3B0aW9ucyh0ICp0ZXN0aW5nLlQsIHNjaGVtYSBzdHJpbmcsIG9wdHMgW11yb2xsLk9wdGlvbiwgZm4gZnVuYyhtaWcgKnJvbGwuUm9sbCwgZGIgKnNxbC5EQikpIHsKCXQuSGVscGVyKCkKCWN0eCA6PSBjb250ZXh0LkJhY2tncm91bmQoKQoKCXREQiwgZXJyIDo9IHNxbC5PcGVuKCJwb3N0Z3JlcyIsIHRDb25uU3RyKQoJaWYgZXJyICE9IG5pbCB7CgkJdC5GYXRhbChlcnIpCgl9CgoJdC5DbGVhbnVwKGZ1bmMoKSB7CgkJaWYgZXJyIDo9IHREQi5DbG9zZSgpOyBlcnIgIT0gbmlsIHsKCQkJdC5GYXRhbGYoIkZhaWxlZCB0byBjbG9zZSBkYXRhYmFzZSBjb25uZWN0aW9uOiAldiIsIGVycikKCQl9Cgl9KQoKCWRiTmFtZSA6PSByYW5kb21EQk5hbWUoKQoKCV8sIGVyciA9IHREQi5FeGVjQ29udGV4dChjdHgsIGZtdC5TcHJpbnRmKCJDUkVBVEUgREFUQUJBU0UgJXMiLCBwcS5RdW90ZUlkZW50aWZpZXIoZGJOYW1lKSkpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCgl1LCBlcnIgOj0gdXJsLlBhcnNlKHRDb25uU3RyKQoJaWYgZXJyICE9IG5pbCB7CgkJdC5GYXRhbChlcnIpCgl9CgoJdS5QYXRoID0gIi8iICsgZGJOYW1lCgljb25uU3RyIDo9IHUuU3RyaW5nKCkKCglkYiwgZXJyIDo9IHNxbC5PcGVuKCJwb3N0Z3JlcyIsIGNvbm5TdHIpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCgl0LkNsZWFudXAoZnVuYygpIHsKCQlpZiBlcnIgOj0gZGIuQ2xvc2UoKTsgZXJyICE9IG5pbCB7CgkJCXQuRmF0YWxmKCJGYWlsZWQgdG8gY2xvc2UgZGF0YWJhc2UgY29ubmVjdGlvbjogJXYiLCBlcnIpCgkJfQoJfSkKCglzdCwgZXJyIDo9IHN0YXRlLk5ldyhjdHgsIGNvbm5TdHIsICJwZ3JvbGwiKQoJaWYgZXJyICE9IG5pbCB7CgkJdC5GYXRhbChlcnIpCgl9CgoJZXJyID0gc3QuSW5pdChjdHgpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCgltaWcsIGVyciA6PSByb2xsLk5ldyhjdHgsIGNvbm5TdHIsIHNjaGVtYSwgc3QsIG9wdHMuLi4pCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCgl0LkNsZWFudXAoZnVuYygpIHsKCQlpZiBlcnIgOj0gbWlnLkNsb3NlKCk7IGVyciAhPSBuaWwgewoJCQl0LkZhdGFsZigiRmFpbGVkIHRvIGNsb3NlIG1pZ3JhdG9yIGNvbm5lY3Rpb246ICV2IiwgZXJyKQoJCX0KCX0pCgoJXywgZXJyID0gZGIuRXhlY0NvbnRleHQoY3R4LCBmbXQuU3ByaW50ZigiQ1JFQVRFIFNDSEVNQSBJRiBOT1QgRVhJU1RTICVzIiwgc2NoZW1hKSkKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCV8sIGVyciA9IGRiLkV4ZWNDb250ZXh0KGN0eCwgZm10LlNwcmludGYoIkdSQU5UIEFMTCBQUklWSUxFR0VTIE9OIFNDSEVNQSAlcyBUTyBwZ3JvbGwiLCBzY2hlbWEpKQoJaWYgZXJyICE9IG5pbCB7CgkJdC5GYXRhbChlcnIpCgl9CgoJXywgZXJyID0gZGIuRXhlY0NvbnRleHQoY3R4LCBmbXQuU3ByaW50ZigiR1JBTlQgQUxMIFBSSVZJTEVHRVMgT04gREFUQUJBU0UgJXMgVE8gcGdyb2xsIiwgZGJOYW1lKSkKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCWZuKG1pZywgZGIpCn0KCmZ1bmMgV2l0aE1pZ3JhdG9ySW5TY2hlbWFBbmRDb25uZWN0aW9uVG9Db250YWluZXIodCAqdGVzdGluZy5ULCBzY2hlbWEgc3RyaW5nLCBmbiBmdW5jKG1pZyAqcm9sbC5Sb2xsLCBkYiAqc3FsLkRCKSkgewoJV2l0aE1pZ3JhdG9ySW5TY2hlbWFBbmRDb25uZWN0aW9uVG9Db250YWluZXJXaXRoT3B0aW9ucyh0LCBzY2hlbWEsIFtdcm9sbC5PcHRpb257cm9sbC5XaXRoTG9ja1RpbWVvdXRNcyg1MDApfSwgZm4pCn0KCmZ1bmMgV2l0aE1pZ3JhdG9yQW5kQ29ubmVjdGlvblRvQ29udGFpbmVyKHQgKnRlc3RpbmcuVCwgZm4gZnVuYyhtaWcgKnJvbGwuUm9sbCwgZGIgKnNxbC5EQikpIHsKCVdpdGhNaWdyYXRvckluU2NoZW1hQW5kQ29ubmVjdGlvblRvQ29udGFpbmVyV2l0aE9wdGlvbnModCwgInB1YmxpYyIsIFtdcm9sbC5PcHRpb257cm9sbC5XaXRoTG9ja1RpbWVvdXRNcyg1MDApfSwgZm4pCn0K" | base64 -d > /tmp/testutils_util.go

RUN cp /tmp/testutils_util.go /workspace/repo/pkg/testutils/util.go

WORKDIR /workspace/repo

RUN go mod download

RUN go build ./... || true

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
