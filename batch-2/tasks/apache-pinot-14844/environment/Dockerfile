FROM eclipse-temurin:11-jdk-jammy

RUN apt-get update && apt-get install -y git patch curl && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/pinot.git && \
    git -c protocol.version=2 fetch --depth 1 --filter=blob:limit=1m origin d6522140501f0653c531acfc90da172ae6cd8ff1 && \
    git remote remove origin && \
    git checkout --detach FETCH_HEAD 2>/dev/null || true; \
    git submodule update --init --recursive --depth 1 2>/dev/null || true; \
    git tag -l | xargs -r git tag -d; \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN ./mvnw install -pl pinot-broker,pinot-core -am -DskipTests \
    -Dcheckstyle.skip -Dspotless.skip -Denforcer.skip -Dlicense.skip \
    -DskipShade -Dmaven.plugin.appassembler.skip=true \
    -T 4 -B -ntp -q || true

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
