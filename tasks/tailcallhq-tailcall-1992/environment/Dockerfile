FROM rust:1.79

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN install -dm 755 /etc/apt/keyrings && \
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg && \
    echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_20.x nodistro main" > /etc/apt/sources.list.d/nodesource.list && \
    apt-get update && apt-get install -y nodejs

RUN npm i -g prettier

RUN curl -LsSf https://get.nexte.st/latest/linux | tar zxf - -C /usr/local/bin

ENV INSTA_UPDATE=no

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/tailcallhq/tailcall.git && \
    git fetch --depth 30 origin ab15abf10feecceea492056645db48ef046bdc00 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN sed -i '/async-graphql-extension-apollo-tracing/d' Cargo.toml

RUN sed -i '/use async_graphql_extension_apollo_tracing/d' src/cli/server/server_config.rs

RUN python3 -c 'import re; f=open("src/cli/server/server_config.rs"); c=f.read(); f.close(); c=re.sub(r"        if let Some\(TelemetryExporter::Apollo\(apollo\)\) = blueprint\.telemetry\.export\.as_ref\(\) \{.*?\}\n", "", c, flags=re.DOTALL); open("src/cli/server/server_config.rs","w").write(c)'

RUN git config user.email "build@build.com" && \
    git config user.name "Build" && \
    git add Cargo.toml src/cli/server/server_config.rs && \
    git commit -m "Remove broken dependency for test environment" && git reset --soft ab15abf10feecceea492056645db48ef046bdc00 && rm -rf .git/logs .git/refs/original 2>/dev/null

RUN cargo fetch

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
