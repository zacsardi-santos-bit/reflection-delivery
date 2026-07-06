FROM eclipse-temurin:21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/solr.git && \
    git fetch --depth 30 origin 011d713a884559e3efeaa69e4f3c8dd8e630ff22 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN chmod +x gradlew && ./gradlew :solr:core:compileTestJava --no-daemon --no-watch-fs 2>&1 | tail -20 || true

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
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# --- offline-harbor gradle warm+offline (idempotent) ---
RUN bash -c "$(echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIC4vZ3JhZGxldyA6c29scjpjb3JlOnRlc3QgLS10ZXN0cyAib3JnLmFwYWNoZS5zb2xyLnNlcnZsZXQuVGVzdFJlcXVlc3RSYXRlTGltaXRlciIgLS1uby1kYWVtb24gLS1uby13YXRjaC1mcyAmJiAoZmluZCAvd29ya3NwYWNlL3JlcG8vc29sci9jb3JlL2J1aWxkL3Rlc3QtcmVzdWx0cyAtbmFtZSAiVEVTVC0qLnhtbCIgLWV4ZWMgY2F0IHt9IFw7IDI+L2Rldi9udWxsIHx8IHRydWUp | base64 -d)" 2>&1 | tail -25 || true
RUN GUH="${GRADLE_USER_HOME:-$HOME/.gradle}"; mkdir -p "$GUH/init.d"; echo Z3JhZGxlLnN0YXJ0UGFyYW1ldGVyLm9mZmxpbmUgPSB0cnVlCmdyYWRsZS50YXNrR3JhcGgud2hlblJlYWR5IHsgZyAtPgogICAgZy5hbGxUYXNrcy5lYWNoIHsgdCAtPgogICAgICAgIHRyeSB7CiAgICAgICAgICAgIGRlZiBjbiA9IHQuZ2V0Q2xhc3MoKS5nZXROYW1lKCkKICAgICAgICAgICAgaWYgKGNuLmNvbnRhaW5zKCdKYXZhZG9jJykgfHwgY24uY29udGFpbnMoJ0Rva2thJykgfHwgY24uY29udGFpbnMoJ0dyb292eWRvYycpIHx8IGNuLmNvbnRhaW5zKCdKYXZhZG9jMicpKSB7CiAgICAgICAgICAgICAgICB0LmVuYWJsZWQgPSBmYWxzZQogICAgICAgICAgICB9CiAgICAgICAgfSBjYXRjaCAoVGhyb3dhYmxlIGlnbm9yZSkge30KICAgIH0KfQo= | base64 -d > "$GUH/init.d/zzz-offline.gradle"; printf 'org.gradle.daemon=false\n' >> "$GUH/gradle.properties"
RUN find /workspace/repo -type d -regex '.*/build/\(test-results\|reports\)' -prune -exec rm -rf {} + 2>/dev/null; true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# OFFLINE-GRADLE-BAKE
RUN cd /workspace/repo && (chmod +x ./gradlew 2>/dev/null; ./gradlew --no-daemon testClasses 2>/dev/null || ./gradlew --no-daemon compileTestJava 2>/dev/null || ./gradlew --no-daemon assemble 2>/dev/null || true)
RUN GUH="${GRADLE_USER_HOME:-$HOME/.gradle}" && mkdir -p "$GUH/init.d" && printf 'gradle.startParameter.offline = true\n' > "$GUH/init.d/zzz-offline.gradle"

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
