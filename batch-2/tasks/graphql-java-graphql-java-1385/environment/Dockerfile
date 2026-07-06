FROM debian:bullseye-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch python3 \
    openjdk-11-jdk \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
ENV PATH="${JAVA_HOME}/bin:${PATH}"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/graphql-java/graphql-java.git && \
    git fetch --depth 30 origin e916b026d7e0a541b5e36733aaf5222e94685d55 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN printf '%s\n' \
    'content = open("/workspace/repo/build.gradle").read()' \
    'lines = content.split("\n")' \
    'result = []' \
    'depth = 0' \
    'in_bintray = False' \
    'for line in lines:' \
    '    s = line.strip()' \
    '    if not in_bintray and s == "bintray {":' \
    '        in_bintray = True' \
    '        depth = 1' \
    '        continue' \
    '    if in_bintray:' \
    '        depth += s.count("{") - s.count("}")' \
    '        if depth <= 0:' \
    '            in_bintray = False' \
    '        continue' \
    '    result.append(line)' \
    'open("/workspace/repo/build.gradle", "w").write("\n".join(result))' \
    > /tmp/fix_bintray.py

RUN sed -i '/^plugins {/,/^}/d' /workspace/repo/build.gradle && python3 /tmp/fix_bintray.py

WORKDIR /workspace/repo

RUN ./gradlew dependencies --no-daemon -q || true

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

RUN find /workspace/repo -type d -regex '.*/build/\(test-results\|reports\)' -prune -exec rm -rf {} + 2>/dev/null; true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# OFFLINE-GRADLE-BAKE
RUN cd /workspace/repo && (chmod +x ./gradlew 2>/dev/null; ./gradlew --no-daemon testClasses 2>/dev/null || ./gradlew --no-daemon compileTestJava 2>/dev/null || ./gradlew --no-daemon assemble 2>/dev/null || true)
RUN GUH="${GRADLE_USER_HOME:-$HOME/.gradle}" && mkdir -p "$GUH/init.d" && printf 'gradle.startParameter.offline = true\n' > "$GUH/init.d/zzz-offline.gradle"

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
