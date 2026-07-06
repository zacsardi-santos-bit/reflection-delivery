FROM postgres:16

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    git curl ca-certificates patch build-essential \
    libpq-dev postgresql-server-dev-16 \
    libreadline6-dev zlib1g-dev pkg-config cmake \
    clang libclang-dev \
    && rm -rf /var/lib/apt/lists/*

ENV CARGO_HOME=/usr/local/cargo
ENV RUSTUP_HOME=/usr/local/rustup
ENV PATH=/usr/lib/postgresql/16/bin:/usr/local/cargo/bin:$PATH

RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | \
    sh -s -- -y --no-modify-path --profile minimal --default-toolchain nightly 2>&1 | tail -5

RUN cargo install cargo-pgrx --version 0.11.3 --locked 2>&1 | tail -5

RUN git config --global --add safe.directory /workspace/repo

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/supabase/pg_graphql.git && \
    git fetch --depth 30 origin eeafeda843dc71a56b25d5860a8fa8b8e8477cee && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cargo pgrx init --pg16 pg_config 2>&1 | tail -5

RUN cargo pgrx install 2>&1 | tail -20

RUN chmod a+w /workspace/repo

RUN printf '#!/bin/bash\nset -o pipefail\ncd /workspace/repo\ngosu postgres bash -c '"'"'cd /workspace/repo && ./bin/installcheck'"'"' | awk '"'"'/^ok [0-9]+ +- /{n=$0;sub(/^ok [0-9]+ +- /,"",n);sub(/ +[0-9]+ ms.*/,"",n);print "PASS [   0.001s] " n} /^not ok [0-9]+ +- /{n=$0;sub(/^not ok [0-9]+ +- /,"",n);sub(/ +[0-9]+ ms.*/,"",n);print "FAIL [   0.001s] " n}'"'"'\n' > /usr/local/bin/run-tests.sh && chmod +x /usr/local/bin/run-tests.sh

WORKDIR /workspace

ENTRYPOINT []
CMD ["sleep", "infinity"]

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
