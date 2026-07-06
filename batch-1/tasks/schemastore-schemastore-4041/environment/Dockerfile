FROM ubuntu:24.04

RUN apt-get update && apt-get install -y \
    git curl ca-certificates gnupg patch \
    && rm -rf /var/lib/apt/lists/*

RUN install -dm 755 /etc/apt/keyrings && \
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg && \
    echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_18.x nodistro main" > /etc/apt/sources.list.d/nodesource.list && \
    apt-get update && apt-get install -y nodejs

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/SchemaStore/schemastore.git && \
    git fetch --depth 30 origin c79e6270ee15b8aa8b4860e7a92682283e079371 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN npm ci 2>&1 | tail -5

RUN echo '#!/bin/bash' > /workspace/check_schema.sh && \
    echo 'cd /workspace/repo' >> /workspace/check_schema.sh && \
    echo 'node ./cli.js check --SchemaName=abc-supply-plan-4.0.0.json 2>&1' >> /workspace/check_schema.sh && \
    echo 'EXIT_CODE=$?' >> /workspace/check_schema.sh && \
    echo 'if [ $EXIT_CODE -eq 0 ]; then' >> /workspace/check_schema.sh && \
    printf '  echo '"'"'<?xml version="1.0"?><testsuite name="schemastore" tests="1" failures="0"><testcase classname="abc-supply-plan-4.0.0" name="schema-check"/></testsuite>'"'"'\n' >> /workspace/check_schema.sh && \
    echo 'else' >> /workspace/check_schema.sh && \
    printf '  echo '"'"'<?xml version="1.0"?><testsuite name="schemastore" tests="1" failures="1"><testcase classname="abc-supply-plan-4.0.0" name="schema-check"><failure message="Schema check failed"/></testcase></testsuite>'"'"'\n' >> /workspace/check_schema.sh && \
    echo 'fi' >> /workspace/check_schema.sh && \
    echo 'exit $EXIT_CODE' >> /workspace/check_schema.sh && \
    chmod +x /workspace/check_schema.sh

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
