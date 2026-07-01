FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    git patch curl ca-certificates \
    openjdk-8-jdk-headless \
    maven \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
ENV PATH="$JAVA_HOME/bin:$PATH"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/seatunnel.git && \
    git fetch --depth 30 origin 4b6c13e8fcdb47fb3de816b879ca55eeb514bf76 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN mvn install -DskipTests -Dlicense.skipAddThirdParty=true --no-transfer-progress \
    -pl seatunnel-config/seatunnel-config-base,seatunnel-config/seatunnel-config-shade,seatunnel-shade,seatunnel-common,seatunnel-api,seatunnel-plugin-discovery,seatunnel-formats,seatunnel-connectors-v2/connector-common,seatunnel-connectors-v2/connector-google-sheets \
    -am 2>&1 | tail -30

RUN find /root/.m2/repository -name "*.lastUpdated" -delete && \
    find /workspace/repo -name "*.class" -delete && \
    find /workspace/repo -name "original-*.jar" -delete && \
    find /workspace/repo -name "*-sources.jar" -delete
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIG12biB0ZXN0IC1wbCBzZWF0dW5uZWwtZm9ybWF0cy9zZWF0dW5uZWwtZm9ybWF0LWF2cm8sc2VhdHVubmVsLWZvcm1hdHMvc2VhdHVubmVsLWZvcm1hdC1qc29uLHNlYXR1bm5lbC1jb25uZWN0b3JzLXYyL2Nvbm5lY3Rvci1nb29nbGUtc2hlZXRzIC1EbGljZW5zZS5za2lwQWRkVGhpcmRQYXJ0eT10cnVlIC0tbm8tdHJhbnNmZXItcHJvZ3Jlc3M= | base64 -d > /workspace/.offline_warm.sh && \
    ( cd /workspace/repo && mvn -B -fn -q -DskipTests -pl 'seatunnel-formats/seatunnel-format-avro,seatunnel-formats/seatunnel-format-json,seatunnel-connectors-v2/connector-google-sheets' -am install -Dcheckstyle.skip=true -Denforcer.skip=true -Dspotbugs.skip=true -Dpmd.skip=true -Dforbiddenapis.skip=true -Dmaven.javadoc.skip=true -Danimal.sniffer.skip=true -Drat.skip=true -Dlicense.skip=true -Dgpg.skip=true -Djacoco.skip=true 2>/dev/null || true ); \
    (timeout 3000 bash /workspace/.offline_warm.sh 2>&1 | tail -25 || true)

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
