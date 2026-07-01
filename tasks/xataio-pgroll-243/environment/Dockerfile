FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    postgresql postgresql-contrib \
    && rm -rf /var/lib/apt/lists/*

RUN PG_VERSION=$(ls /etc/postgresql/) \
    && PG_CONF="/etc/postgresql/${PG_VERSION}/main/postgresql.conf" \
    && PG_HBA="/etc/postgresql/${PG_VERSION}/main/pg_hba.conf" \
    && sed -i "s/#listen_addresses = 'localhost'/listen_addresses = '*'/" "$PG_CONF" \
    && echo "host all all 0.0.0.0/0 md5" >> "$PG_HBA" \
    && service postgresql start \
    && su -c "psql -c \"ALTER USER postgres PASSWORD 'postgres';\"" postgres \
    && service postgresql stop

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/xataio/pgroll.git && \
    git fetch --depth 30 origin 994a1aa6f8142cd2078ac12f625728ce7474159f && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN echo 'Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEFwYWNoZS0yLjAKCnBhY2thZ2UgdGVzdHV0aWxzCgppbXBvcnQgKAoJImNvbnRleHQiCgkiZGF0YWJhc2Uvc3FsIgoJImZtdCIKCSJsb2ciCgkibmV0L3VybCIKCSJvcyIKCSJ0ZXN0aW5nIgoKCSJnaXRodWIuY29tL2xpYi9wcSIKCSJnaXRodWIuY29tL3hhdGFpby9wZ3JvbGwvcGtnL3JvbGwiCgkiZ2l0aHViLmNvbS94YXRhaW8vcGdyb2xsL3BrZy9zdGF0ZSIKKQoKdmFyIHRDb25uU3RyIHN0cmluZwoKZnVuYyBTaGFyZWRUZXN0TWFpbihtICp0ZXN0aW5nLk0pIHsKCWN0eCA6PSBjb250ZXh0LkJhY2tncm91bmQoKQoKCXRDb25uU3RyID0gInBvc3RncmVzOi8vcG9zdGdyZXM6cG9zdGdyZXNAbG9jYWxob3N0OjU0MzIvcG9zdGdyZXM/c3NsbW9kZT1kaXNhYmxlIgoKCWRiLCBlcnIgOj0gc3FsLk9wZW4oInBvc3RncmVzIiwgdENvbm5TdHIpCglpZiBlcnIgIT0gbmlsIHsKCQlsb2cuUHJpbnRmKCJmYWlsZWQgdG8gb3BlbiBkYXRhYmFzZTogJXYiLCBlcnIpCgkJb3MuRXhpdCgxKQoJfQoJZGVmZXIgZGIuQ2xvc2UoKQoKCWlmIGVyciA6PSBkYi5QaW5nQ29udGV4dChjdHgpOyBlcnIgIT0gbmlsIHsKCQlsb2cuUHJpbnRmKCJmYWlsZWQgdG8gcGluZyBkYXRhYmFzZTogJXYiLCBlcnIpCgkJb3MuRXhpdCgxKQoJfQoKCV8sIF8gPSBkYi5FeGVjQ29udGV4dChjdHgsICJDUkVBVEUgUk9MRSBwZ3JvbGwiKQoKCWV4aXRDb2RlIDo9IG0uUnVuKCkKCW9zLkV4aXQoZXhpdENvZGUpCn0KCmZ1bmMgV2l0aFN0YXRlQW5kQ29ubmVjdGlvblRvQ29udGFpbmVyKHQgKnRlc3RpbmcuVCwgZm4gZnVuYygqc3RhdGUuU3RhdGUsICpzcWwuREIpKSB7Cgl0LkhlbHBlcigpCgljdHggOj0gY29udGV4dC5CYWNrZ3JvdW5kKCkKCgl0REIsIGVyciA6PSBzcWwuT3BlbigicG9zdGdyZXMiLCB0Q29ublN0cikKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCXQuQ2xlYW51cChmdW5jKCkgewoJCWlmIGVyciA6PSB0REIuQ2xvc2UoKTsgZXJyICE9IG5pbCB7CgkJCXQuRmF0YWxmKCJGYWlsZWQgdG8gY2xvc2UgZGF0YWJhc2UgY29ubmVjdGlvbjogJXYiLCBlcnIpCgkJfQoJfSkKCglkYk5hbWUgOj0gcmFuZG9tREJOYW1lKCkKCglfLCBlcnIgPSB0REIuRXhlY0NvbnRleHQoY3R4LCBmbXQuU3ByaW50ZigiQ1JFQVRFIERBVEFCQVNFICVzIiwgcHEuUXVvdGVJZGVudGlmaWVyKGRiTmFtZSkpKQoJaWYgZXJyICE9IG5pbCB7CgkJdC5GYXRhbChlcnIpCgl9CgoJdSwgZXJyIDo9IHVybC5QYXJzZSh0Q29ublN0cikKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCXUuUGF0aCA9ICIvIiArIGRiTmFtZQoJY29ublN0ciA6PSB1LlN0cmluZygpCgoJc3QsIGVyciA6PSBzdGF0ZS5OZXcoY3R4LCBjb25uU3RyLCAicGdyb2xsIikKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCWRiLCBlcnIgOj0gc3FsLk9wZW4oInBvc3RncmVzIiwgY29ublN0cikKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCXQuQ2xlYW51cChmdW5jKCkgewoJCWlmIGVyciA6PSBkYi5DbG9zZSgpOyBlcnIgIT0gbmlsIHsKCQkJdC5GYXRhbGYoIkZhaWxlZCB0byBjbG9zZSBkYXRhYmFzZSBjb25uZWN0aW9uOiAldiIsIGVycikKCQl9Cgl9KQoKCWZuKHN0LCBkYikKfQoKZnVuYyBXaXRoTWlncmF0b3JJblNjaGVtYUFuZENvbm5lY3Rpb25Ub0NvbnRhaW5lcldpdGhPcHRpb25zKHQgKnRlc3RpbmcuVCwgc2NoZW1hIHN0cmluZywgb3B0cyBbXXJvbGwuT3B0aW9uLCBmbiBmdW5jKG1pZyAqcm9sbC5Sb2xsLCBkYiAqc3FsLkRCKSkgewoJdC5IZWxwZXIoKQoJY3R4IDo9IGNvbnRleHQuQmFja2dyb3VuZCgpCgoJdERCLCBlcnIgOj0gc3FsLk9wZW4oInBvc3RncmVzIiwgdENvbm5TdHIpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCgl0LkNsZWFudXAoZnVuYygpIHsKCQlpZiBlcnIgOj0gdERCLkNsb3NlKCk7IGVyciAhPSBuaWwgewoJCQl0LkZhdGFsZigiRmFpbGVkIHRvIGNsb3NlIGRhdGFiYXNlIGNvbm5lY3Rpb246ICV2IiwgZXJyKQoJCX0KCX0pCgoJZGJOYW1lIDo9IHJhbmRvbURCTmFtZSgpCgoJXywgZXJyID0gdERCLkV4ZWNDb250ZXh0KGN0eCwgZm10LlNwcmludGYoIkNSRUFURSBEQVRBQkFTRSAlcyIsIHBxLlF1b3RlSWRlbnRpZmllcihkYk5hbWUpKSkKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCXUsIGVyciA6PSB1cmwuUGFyc2UodENvbm5TdHIpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCgl1LlBhdGggPSAiLyIgKyBkYk5hbWUKCWNvbm5TdHIgOj0gdS5TdHJpbmcoKQoKCWRiLCBlcnIgOj0gc3FsLk9wZW4oInBvc3RncmVzIiwgY29ublN0cikKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCXQuQ2xlYW51cChmdW5jKCkgewoJCWlmIGVyciA6PSBkYi5DbG9zZSgpOyBlcnIgIT0gbmlsIHsKCQkJdC5GYXRhbGYoIkZhaWxlZCB0byBjbG9zZSBkYXRhYmFzZSBjb25uZWN0aW9uOiAldiIsIGVycikKCQl9Cgl9KQoKCXN0LCBlcnIgOj0gc3RhdGUuTmV3KGN0eCwgY29ublN0ciwgInBncm9sbCIpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCgllcnIgPSBzdC5Jbml0KGN0eCkKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCW1pZywgZXJyIDo9IHJvbGwuTmV3KGN0eCwgY29ublN0ciwgc2NoZW1hLCBzdCwgb3B0cy4uLikKCWlmIGVyciAhPSBuaWwgewoJCXQuRmF0YWwoZXJyKQoJfQoKCXQuQ2xlYW51cChmdW5jKCkgewoJCWlmIGVyciA6PSBtaWcuQ2xvc2UoKTsgZXJyICE9IG5pbCB7CgkJCXQuRmF0YWxmKCJGYWlsZWQgdG8gY2xvc2UgbWlncmF0b3IgY29ubmVjdGlvbjogJXYiLCBlcnIpCgkJfQoJfSkKCglfLCBlcnIgPSBkYi5FeGVjQ29udGV4dChjdHgsIGZtdC5TcHJpbnRmKCJDUkVBVEUgU0NIRU1BIElGIE5PVCBFWElTVFMgJXMiLCBzY2hlbWEpKQoJaWYgZXJyICE9IG5pbCB7CgkJdC5GYXRhbChlcnIpCgl9CgoJXywgZXJyID0gZGIuRXhlY0NvbnRleHQoY3R4LCBmbXQuU3ByaW50ZigiR1JBTlQgQUxMIFBSSVZJTEVHRVMgT04gU0NIRU1BICVzIFRPIHBncm9sbCIsIHNjaGVtYSkpCglpZiBlcnIgIT0gbmlsIHsKCQl0LkZhdGFsKGVycikKCX0KCglfLCBlcnIgPSBkYi5FeGVjQ29udGV4dChjdHgsIGZtdC5TcHJpbnRmKCJHUkFOVCBBTEwgUFJJVklMRUdFUyBPTiBEQVRBQkFTRSAlcyBUTyBwZ3JvbGwiLCBkYk5hbWUpKQoJaWYgZXJyICE9IG5pbCB7CgkJdC5GYXRhbChlcnIpCgl9CgoJZm4obWlnLCBkYikKfQoKZnVuYyBXaXRoTWlncmF0b3JJblNjaGVtYUFuZENvbm5lY3Rpb25Ub0NvbnRhaW5lcih0ICp0ZXN0aW5nLlQsIHNjaGVtYSBzdHJpbmcsIGZuIGZ1bmMobWlnICpyb2xsLlJvbGwsIGRiICpzcWwuREIpKSB7CglXaXRoTWlncmF0b3JJblNjaGVtYUFuZENvbm5lY3Rpb25Ub0NvbnRhaW5lcldpdGhPcHRpb25zKHQsIHNjaGVtYSwgW11yb2xsLk9wdGlvbntyb2xsLldpdGhMb2NrVGltZW91dE1zKDUwMCl9LCBmbikKfQoKZnVuYyBXaXRoTWlncmF0b3JBbmRDb25uZWN0aW9uVG9Db250YWluZXIodCAqdGVzdGluZy5ULCBmbiBmdW5jKG1pZyAqcm9sbC5Sb2xsLCBkYiAqc3FsLkRCKSkgewoJV2l0aE1pZ3JhdG9ySW5TY2hlbWFBbmRDb25uZWN0aW9uVG9Db250YWluZXJXaXRoT3B0aW9ucyh0LCAicHVibGljIiwgW11yb2xsLk9wdGlvbntyb2xsLldpdGhMb2NrVGltZW91dE1zKDUwMCl9LCBmbikKfQo=' \
    | base64 -d > /workspace/repo/pkg/testutils/util.go \
    && git config user.email "ci@test.local" \
    && git config user.name "CI" \
    && git add pkg/testutils/util.go \
    && git commit -m "replace util.go to use local postgres instead of testcontainers" && git reset --soft 994a1aa6f8142cd2078ac12f625728ce7474159f && rm -rf .git/logs .git/refs/original 2>/dev/null

RUN go mod download

RUN go build -v ./... || true

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
