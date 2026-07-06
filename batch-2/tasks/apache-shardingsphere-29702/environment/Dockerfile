FROM ubuntu:24.04

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    openjdk-17-jdk maven \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH
ENV MAVEN_OPTS="-Xmx2g -Dhttp.keepAlive=false -Dmaven.wagon.http.pool=false -Dmaven.wagon.http.retryHandler.class=standard -Dmaven.wagon.http.retryHandler.count=3"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/shardingsphere.git && \
    git -c protocol.version=2 fetch --depth 1 --filter=blob:limit=1m origin a64caed487a656ac562977e7ccbaec6e3a15a610 && \
    git remote remove origin && \
    git checkout --detach FETCH_HEAD 2>/dev/null || true; \
    git submodule update --init --recursive --depth 1 2>/dev/null || true; \
    git tag -l | xargs -r git tag -d; \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN mvn install -pl parser/sql/dialect/sqlserver -am -DskipTests \
    -Dspotless.apply.skip=true -Dcheckstyle.skip=true -q
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIG12biBpbnN0YWxsIC1wbCB0ZXN0L2l0L3BhcnNlcixwYXJzZXIvc3FsL3N0YXRlbWVudCAtRHNraXBUZXN0cyAtRHNwb3RsZXNzLmFwcGx5LnNraXA9dHJ1ZSAtRGNoZWNrc3R5bGUuc2tpcD10cnVlICYmIG12biB2ZXJpZnkgLXBsIHBhcnNlci9zcWwvZGlhbGVjdC9zcWxzZXJ2ZXIgLURzcG90bGVzcy5hcHBseS5za2lwPXRydWUgLURjaGVja3N0eWxlLnNraXA9dHJ1ZQ== | base64 -d > /workspace/.offline_warm.sh && \
    ( cd /workspace/repo && mvn -B -fn -q -DskipTests -pl 'test/it/parser,parser/sql/statement' -am install -Dcheckstyle.skip=true -Denforcer.skip=true -Dspotbugs.skip=true -Dpmd.skip=true -Dforbiddenapis.skip=true -Dmaven.javadoc.skip=true -Danimal.sniffer.skip=true -Drat.skip=true -Dlicense.skip=true -Dgpg.skip=true -Djacoco.skip=true 2>/dev/null || true ); \
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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh

# offline-harbor: git blob-drop shrink (client-contract .git<100MB, HEAD==base)
RUN set +e; cd /workspace/repo 2>/dev/null && ( rm -rf .git/modules .git/worktrees 2>/dev/null; git reflog expire --expire=now --all 2>/dev/null; rm -rf .git/logs; git remote remove origin 2>/dev/null; git tag -l | xargs -r git tag -d >/dev/null 2>&1; git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d 2>/dev/null; git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_keep.txt; if [ -s /tmp/_keep.txt ]; then git pack-objects --quiet /tmp/_kp < /tmp/_keep.txt >/dev/null 2>&1 && rm -rf .git/objects/pack && mkdir -p .git/objects/pack && find .git/objects -maxdepth 1 -type d -name '[0-9a-f][0-9a-f]' -exec rm -rf {} + 2>/dev/null; mv /tmp/_kp-*.pack /tmp/_kp-*.idx .git/objects/pack/ 2>/dev/null; fi; git rev-parse HEAD >/dev/null 2>&1 || echo "WARN head-broken"; du -sm .git 2>/dev/null | cut -f1 ) ; true
