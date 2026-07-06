FROM eclipse-temurin:17-jdk-focal

RUN apt-get update && apt-get install -y git curl && rm -rf /var/lib/apt/lists/*

ENV MAVEN_VERSION=3.9.6
ENV MAVEN_HOME=/opt/maven
ENV PATH=/opt/maven/bin:$PATH
ENV MAVEN_OPTS="-Xmx2g -Xms512m -Dhttp.keepAlive=false -Dmaven.wagon.http.pool=false"

RUN curl -fsSL https://archive.apache.org/dist/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz | tar -xzC /opt && \
    mv /opt/apache-maven-3.9.6 /opt/maven

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/seatunnel.git && \
    git fetch --depth 30 origin 6c658056993006bb5429e9af58107c29d4f864dd && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN /opt/maven/bin/mvn install \
    -pl seatunnel-connectors-v2/connector-cdc/connector-cdc-mysql \
    -am \
    -DskipTests \
    -Dcheckstyle.skip=true \
    -Dlicense.skipAddThirdParty=true \
    -Dmaven.javadoc.skip=true \
    --batch-mode \
    --no-transfer-progress

RUN /opt/maven/bin/mvn dependency:get \
    -Dartifact=org.apache.maven.surefire:surefire-junit-platform:2.22.2 \
    --batch-mode \
    --no-transfer-progress

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
