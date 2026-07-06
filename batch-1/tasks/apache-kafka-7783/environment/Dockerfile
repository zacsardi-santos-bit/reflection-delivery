FROM eclipse-temurin:11-jdk

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/kafka.git && \
    git fetch --depth 30 origin bddc4c109a94bda16265b573df71a6b2450a8bbd && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN mkdir -p /tmp/stub/org/ajoberstar/grgit && \
    printf 'package org.ajoberstar.grgit;\nimport java.util.*;\npublic class Grgit {\n    public static Grgit open(Map p){return new Grgit();}\n    public List clean(Map p){return new ArrayList();}\n}\n' \
        > /tmp/stub/org/ajoberstar/grgit/Grgit.java && \
    javac /tmp/stub/org/ajoberstar/grgit/Grgit.java -d /tmp/stub && \
    mkdir -p /root/.m2/repository/org/ajoberstar/grgit/grgit-core/3.1.1 && \
    cd /tmp/stub && jar cf /root/.m2/repository/org/ajoberstar/grgit/grgit-core/3.1.1/grgit-core-3.1.1.jar org && \
    printf '<project><modelVersion>4.0.0</modelVersion><groupId>org.ajoberstar.grgit</groupId><artifactId>grgit-core</artifactId><version>3.1.1</version></project>' \
        > /root/.m2/repository/org/ajoberstar/grgit/grgit-core/3.1.1/grgit-core-3.1.1.pom

RUN mkdir -p /root/.gradle/init.d && \
    printf 'allprojects {\n  buildscript {\n    repositories {\n      mavenLocal()\n    }\n  }\n}\n' \
        > /root/.gradle/init.d/local-grgit.gradle

RUN printf 'import xml.etree.ElementTree as ET\nimport glob\nfor f in sorted(glob.glob("/workspace/repo/connect/runtime/build/test-results/test/TEST-*.xml")):\n    root = ET.parse(f).getroot()\n    print("Tests run: {}, Failures: {}, Errors: {}, Skipped: {}".format(root.get("tests","0"),root.get("failures","0"),root.get("errors","0"),root.get("skipped","0")))\n' \
    > /usr/local/bin/print-test-results.py

WORKDIR /workspace/repo

RUN ./gradlew :connect:runtime:testClasses --no-daemon -x rat || true
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIC4vZ3JhZGxldyA6Y29ubmVjdDpydW50aW1lOnRlc3QgLS10ZXN0cyAib3JnLmFwYWNoZS5rYWZrYS5jb25uZWN0LnJ1bnRpbWUuZGlzdHJpYnV0ZWQuSW5jcmVtZW50YWxDb29wZXJhdGl2ZUFzc2lnbm9yVGVzdCIgLS1uby1kYWVtb24gLXggcmF0ICYmIHB5dGhvbjMgL3Vzci9sb2NhbC9iaW4vcHJpbnQtdGVzdC1yZXN1bHRzLnB5 | base64 -d > /workspace/.offline_warm.sh && \
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
