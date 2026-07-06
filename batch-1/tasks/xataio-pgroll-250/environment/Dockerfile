FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    postgresql python3 \
    && rm -rf /var/lib/apt/lists/*

RUN sed -i 's/peer/trust/g; s/scram-sha-256/trust/g; s/md5/trust/g' /etc/postgresql/*/main/pg_hba.conf

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/xataio/pgroll.git && \
    git fetch --depth 30 origin f798ba586ffcdd4f767ae27310c56ac7d1784e78 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN echo 'bGluZXMgPSBvcGVuKCcvd29ya3NwYWNlL3JlcG8vcGtnL3Rlc3R1dGlscy91dGlsLmdvJykucmVhZGxpbmVzKCkKbmV3X2xpbmVzID0gW10KaW5zZXJ0ZWQgPSBGYWxzZQpmb3IgbGluZSBpbiBsaW5lczoKICAgIGlmIG5vdCBpbnNlcnRlZCBhbmQgJ1x0d2FpdEZvckxvZ3MgOj0gd2FpdC4nIGluIGxpbmU6CiAgICAgICAgbmV3X2xpbmVzLmFwcGVuZCgnXHRpZiBkaXJlY3RVUkwgOj0gb3MuR2V0ZW52KCJQT1NUR1JFU19ESVJFQ1RfVVJMIik7IGRpcmVjdFVSTCAhPSAiIiB7XG4nKQogICAgICAgIG5ld19saW5lcy5hcHBlbmQoJ1x0XHR0Q29ublN0ciA9IGRpcmVjdFVSTFxuJykKICAgICAgICBuZXdfbGluZXMuYXBwZW5kKCdcdFx0ZGIsIF8gOj0gc3FsLk9wZW4oInBvc3RncmVzIiwgdENvbm5TdHIpXG4nKQogICAgICAgIG5ld19saW5lcy5hcHBlbmQoJ1x0XHRpZiBkYiAhPSBuaWwge1xuJykKICAgICAgICBuZXdfbGluZXMuYXBwZW5kKCdcdFx0XHRkYi5FeGVjQ29udGV4dChjdHgsICJDUkVBVEUgUk9MRSBwZ3JvbGwiKVxuJykKICAgICAgICBuZXdfbGluZXMuYXBwZW5kKCdcdFx0XHRkYi5DbG9zZSgpXG4nKQogICAgICAgIG5ld19saW5lcy5hcHBlbmQoJ1x0XHR9XG4nKQogICAgICAgIG5ld19saW5lcy5hcHBlbmQoJ1x0XHRvcy5FeGl0KG0uUnVuKCkpXG4nKQogICAgICAgIG5ld19saW5lcy5hcHBlbmQoJ1x0XHRyZXR1cm5cbicpCiAgICAgICAgbmV3X2xpbmVzLmFwcGVuZCgnXHR9XG4nKQogICAgICAgIG5ld19saW5lcy5hcHBlbmQoJ1xuJykKICAgICAgICBpbnNlcnRlZCA9IFRydWUKICAgIG5ld19saW5lcy5hcHBlbmQobGluZSkKb3BlbignL3dvcmtzcGFjZS9yZXBvL3BrZy90ZXN0dXRpbHMvdXRpbC5nbycsICd3Jykud3JpdGVsaW5lcyhuZXdfbGluZXMpCg==' | base64 -d | python3 && \
    git config user.email "build@ci.local" && \
    git config user.name "CI Build" && \
    git -c commit.gpgsign=false add pkg/testutils/util.go && \
    git -c commit.gpgsign=false commit --amend --no-edit

RUN go mod download

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

# --- offline-harbor go-bake v1 ---
RUN set +e; cd /workspace/repo && for gm in $(find . -name go.mod -not -path '*/vendor/*' -not -path '*/testdata/*' -not -path '*/node_modules/*' 2>/dev/null); do d=$(dirname "$gm"); ( cd "$d" && GOWORK=off GOFLAGS=-mod=mod go mod download all 2>/dev/null ); done; true
RUN set +e; cd /workspace/repo 2>/dev/null || cd /workspace/repo; GOWORK=off GOFLAGS=-mod=mod go test -count=1 -run '^$' ./pkg/migrations 2>/dev/null; true
ENV GOWORK=off
ENV GOFLAGS=-mod=mod
ENV GOPROXY=off
ENV GOSUMDB=off

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
