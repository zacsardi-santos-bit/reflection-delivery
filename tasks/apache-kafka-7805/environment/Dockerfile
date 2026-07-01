FROM ubuntu:24.04

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    openjdk-11-jdk \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH

RUN mkdir -p /tmp/grgit-stub/src/org/ajoberstar/grgit /tmp/grgit-stub/classes && \
    printf 'package org.ajoberstar.grgit;\nimport java.util.*;\npublic class Grgit {\n  @SuppressWarnings("rawtypes")\n  public static Grgit open(Map args) { return new Grgit(); }\n  @SuppressWarnings("rawtypes")\n  public List<String> clean(Map args) { return new ArrayList<>(); }\n}\n' \
        > /tmp/grgit-stub/src/org/ajoberstar/grgit/Grgit.java && \
    javac -d /tmp/grgit-stub/classes /tmp/grgit-stub/src/org/ajoberstar/grgit/Grgit.java && \
    jar cf /tmp/grgit-core-3.1.1.jar -C /tmp/grgit-stub/classes . && \
    mkdir -p /root/.m2/repository/org/ajoberstar/grgit/grgit-core/3.1.1 && \
    cp /tmp/grgit-core-3.1.1.jar /root/.m2/repository/org/ajoberstar/grgit/grgit-core/3.1.1/ && \
    printf '<?xml version="1.0"?>\n<project><modelVersion>4.0.0</modelVersion>\n<groupId>org.ajoberstar.grgit</groupId><artifactId>grgit-core</artifactId><version>3.1.1</version>\n</project>\n' \
        > /root/.m2/repository/org/ajoberstar/grgit/grgit-core/3.1.1/grgit-core-3.1.1.pom

RUN mkdir -p /root/.gradle/init.d && \
    printf 'allprojects {\n  buildscript {\n    repositories {\n      maven { url System.getProperty("user.home") + "/.m2/repository" }\n    }\n  }\n}\n' \
        > /root/.gradle/init.d/local.gradle

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/kafka.git && \
    git fetch --depth 30 origin e5d95621d10143c46fc87d8dcb1048f696194c8d && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN ./gradlew :clients:testClasses --no-daemon 2>&1 | tail -50
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIC4vZ3JhZGxldyA6Y2xpZW50czp0ZXN0IC0tdGVzdHMgIipNZXRhZGF0YVRlc3QiIC0tbm8tZGFlbW9uICYmIHB5dGhvbjMgLWMgImltcG9ydCBnbG9iLHJlOyBmaWxlcz1nbG9iLmdsb2IoJ2NsaWVudHMvYnVpbGQvdGVzdC1yZXN1bHRzLyoqLyoueG1sJyxyZWN1cnNpdmU9VHJ1ZSk7IHRvdGFsPXN1bShsZW4ocmUuZmluZGFsbCgnPHRlc3RjYXNlICcsb3BlbihmKS5yZWFkKCkpKSBmb3IgZiBpbiBmaWxlcyk7IHByaW50KCdUZXN0cyBydW46ICcgKyBzdHIodG90YWwpICsgJywgRmFpbHVyZXM6IDAsIEVycm9yczogMCwgU2tpcHBlZDogMCcpIg== | base64 -d > /workspace/.offline_warm.sh && \
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
