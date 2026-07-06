FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    openjdk-11-jdk openjdk-17-jdk openjdk-21-jdk \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH
ENV JAVA_11_HOME=/usr/lib/jvm/java-11-openjdk-amd64
ENV JAVA_17_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV JAVA_21_HOME=/usr/lib/jvm/java-21-openjdk-amd64

RUN curl -L "https://api.adoptium.net/v3/binary/latest/8/ga/linux/x64/jdk/hotspot/normal/eclipse" -o /tmp/jdk8.tar.gz && \
    mkdir -p /opt/java/8 && \
    tar xzf /tmp/jdk8.tar.gz -C /opt/java/8 --strip-components=1 && \
    rm /tmp/jdk8.tar.gz

ENV JAVA_8_HOME=/opt/java/8

RUN mkdir -p /root/.gradle/init.d && printf 'allprojects {\n  tasks.withType(Test) {\n    testLogging {\n      events "passed", "failed", "skipped"\n    }\n    afterSuite { suite, result ->\n      if (suite.parent != null) {\n        def total = result.testCount\n        def failures = result.failedTestCount\n        def skipped = result.skippedTestCount\n        println "Tests run: ${total}, Failures: ${failures}, Errors: 0, Skipped: ${skipped}"\n      }\n    }\n  }\n}\n' > /root/.gradle/init.d/test-logging.gradle

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/DataDog/dd-trace-java.git && \
    git fetch --depth 1 origin 917b7d9e4fb2325e14d4a7797bcb2a701c627fdd && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN ./gradlew :internal-api:dependencies --no-daemon --configure-on-demand -x spotlessCheck 2>&1 | tail -10 || true
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo ZWNobyAnW0lORk9dIEJ1aWxkaW5nIGludGVybmFsLWFwaSBbMS8xXScgJiYgKHNldCAtbyBwaXBlZmFpbDsgLi9ncmFkbGV3IDppbnRlcm5hbC1hcGk6dGVzdCAtLXRlc3RzIGRhdGFkb2cudHJhY2UuYm9vdHN0cmFwLmNvbmZpZy5wcm92aWRlci5PdGVsRW52aXJvbm1lbnRDb25maWdTb3VyY2VUZXN0IC0tbm8tZGFlbW9uIC0tY29uZmlndXJlLW9uLWRlbWFuZCAteCBzcG90bGVzc0NoZWNrIC14IHNwb3RidWdzTWFpbiAteCBzcG90YnVnc1Rlc3QgLXggamF2YWRvY0phciAteCBzb3VyY2VzSmFyIC14IGNvZGVuYXJjVGVzdCAteCBjb2RlbmFyY01haW4gMj4mMSB8IGdyZXAgLXYgJ0JVSUxEIEZBSUwnKQ== | base64 -d > /workspace/.offline_warm.sh && \
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
