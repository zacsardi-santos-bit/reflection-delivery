FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y git openjdk-17-jdk maven patch && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH="$JAVA_HOME/bin:$PATH"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/pinot.git && \
    git -c protocol.version=2 fetch --depth 1 --filter=blob:limit=1m origin 0be51ca9558580e5f03979a1b65972459d83aaf7 && \
    git remote remove origin && \
    git checkout --detach FETCH_HEAD 2>/dev/null || true; \
    git submodule update --init --recursive --depth 1 2>/dev/null || true; \
    git tag -l | xargs -r git tag -d; \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

ENV MAVEN_OPTS="-Xmx2G -DskipShade -DfailIfNoTests=false -Dmaven.wagon.httpconnectionManager.ttlSeconds=25 -Dmaven.wagon.http.retryHandler.count=30 -Dhttp.keepAlive=false -Dmaven.wagon.http.pool=false"

RUN mvn install -pl pinot-core,pinot-segment-local --also-make -DskipTests -B -ntp -q

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

# --- offline-harbor: make the local hostname resolvable (surefire fork channel) ---
# Maven Surefire 3.x's fork channel calls InetAddress.getLocalHost(); offline this
# throws UnknownHostException when the runtime hostname is not in /etc/hosts. Map it
# to loopback so forked test JVMs can connect back without DNS.
RUN { echo '127.0.0.1 localhost'; echo '127.0.0.1 modal'; echo "127.0.0.1 $(cat /etc/hostname 2>/dev/null)"; echo '::1 localhost ip6-localhost ip6-loopback'; } >> /etc/hosts 2>/dev/null || true

# --- offline-harbor: bake Maven deps into ~/.m2 (build-time warm) ---
# Warm the local Maven repo with EXACTLY the deps the oracle (base+test.patch+golden)
# needs, so the test runs offline. Patches are applied only to warm ~/.m2 and are

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh

# offline-harbor: git blob-drop shrink (client-contract .git<100MB, HEAD==base)
RUN set +e; cd /workspace/repo 2>/dev/null && ( rm -rf .git/modules .git/worktrees 2>/dev/null; git reflog expire --expire=now --all 2>/dev/null; rm -rf .git/logs; git remote remove origin 2>/dev/null; git tag -l | xargs -r git tag -d >/dev/null 2>&1; git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d 2>/dev/null; git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_keep.txt; if [ -s /tmp/_keep.txt ]; then git pack-objects --quiet /tmp/_kp < /tmp/_keep.txt >/dev/null 2>&1 && rm -rf .git/objects/pack && mkdir -p .git/objects/pack && find .git/objects -maxdepth 1 -type d -name '[0-9a-f][0-9a-f]' -exec rm -rf {} + 2>/dev/null; mv /tmp/_kp-*.pack /tmp/_kp-*.idx .git/objects/pack/ 2>/dev/null; fi; git rev-parse HEAD >/dev/null 2>&1 || echo "WARN head-broken"; du -sm .git 2>/dev/null | cut -f1 ) ; true
