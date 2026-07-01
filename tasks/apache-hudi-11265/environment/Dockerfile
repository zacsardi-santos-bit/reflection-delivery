FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    openjdk-17-jdk maven \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/hudi.git && \
    git fetch --depth 30 origin c9c8ae4e879934e7f7f00a586575841625f43432 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN cd /workspace/repo && \
    sed -i 's/\r//' hudi-spark-datasource/hudi-spark/src/test/java/org/apache/hudi/functional/TestSparkSortAndSizeClustering.java && \
    git config user.email "build@fix.com" && \
    git config user.name "Build" && \
    git add hudi-spark-datasource/hudi-spark/src/test/java/org/apache/hudi/functional/TestSparkSortAndSizeClustering.java && \
    git commit -m "Convert TestSparkSortAndSizeClustering line endings to LF"

RUN cd /workspace/repo && mvn -pl hudi-common install -DskipTests -am -Punit-tests -Pjava17 -Davro.version=1.11.2 -q
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIG12biAtcGwgaHVkaS1jb21tb24gdGVzdCAtUHVuaXQtdGVzdHMgLVBqYXZhMTcgLURhdnJvLnZlcnNpb249MS4xMS4yIC1EdGVzdD1UZXN0TWVyY2lmdWxKc29uQ29udmVydGVyIC1EZmFpbElmTm9UZXN0cz1mYWxzZQ== | base64 -d > /workspace/.offline_warm.sh && \
    ( cd /workspace/repo && mvn -B -fn -q -DskipTests -pl 'hudi-common' -am install -Dcheckstyle.skip=true -Denforcer.skip=true -Dspotbugs.skip=true -Dpmd.skip=true -Dforbiddenapis.skip=true -Dmaven.javadoc.skip=true -Danimal.sniffer.skip=true -Drat.skip=true -Dlicense.skip=true -Dgpg.skip=true -Djacoco.skip=true 2>/dev/null || true ); \
    (timeout 3000 bash /workspace/.offline_warm.sh 2>&1 | tail -25 || true)

WORKDIR /workspace

# >>> final-git-hygiene (compliance) >>>
RUN cd /workspace/repo 2>/dev/null && ( \
    B=c9c8ae4e879934e7f7f00a586575841625f43432; H=$(git rev-parse HEAD 2>/dev/null); \
    if [ "$H" != "$B" ]; then git reset --soft "$B" 2>/dev/null || true; fi; \
    git remote remove origin 2>/dev/null || true; \
    git tag -l 2>/dev/null | xargs -r git tag -d 2>/dev/null || true; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    git gc --prune=now 2>/dev/null || true; \
    rm -rf .git/logs ) ; true
# <<< final-git-hygiene (compliance) <<<

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
