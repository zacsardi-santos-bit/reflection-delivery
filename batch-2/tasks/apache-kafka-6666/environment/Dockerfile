FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential unzip \
    openjdk-11-jdk \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
ENV PATH=$JAVA_HOME/bin:$PATH

RUN curl -fsSL https://services.gradle.org/distributions/gradle-5.4.1-bin.zip -o /tmp/gradle.zip \
    && unzip -q /tmp/gradle.zip -d /opt \
    && rm /tmp/gradle.zip

ENV GRADLE_HOME=/opt/gradle-5.4.1
ENV PATH=$GRADLE_HOME/bin:$PATH

RUN mkdir -p /tmp/grgit-src/org/ajoberstar/grgit /tmp/grgit-classes \
    && printf 'package org.ajoberstar.grgit;\nimport java.util.*;\npublic class Grgit {\n  public static Grgit open(Map args) { return new Grgit(); }\n  public List clean(Map args) { return Collections.emptyList(); }\n  public void close() {}\n}\n' > /tmp/grgit-src/org/ajoberstar/grgit/Grgit.java \
    && javac -source 8 -target 8 /tmp/grgit-src/org/ajoberstar/grgit/Grgit.java -d /tmp/grgit-classes \
    && mkdir -p /workspace/local-repo/org/ajoberstar/grgit/grgit-core/3.1.1 \
    && jar cf /workspace/local-repo/org/ajoberstar/grgit/grgit-core/3.1.1/grgit-core-3.1.1.jar -C /tmp/grgit-classes . \
    && printf '<?xml version="1.0" encoding="UTF-8"?>\n<project>\n  <modelVersion>4.0.0</modelVersion>\n  <groupId>org.ajoberstar.grgit</groupId>\n  <artifactId>grgit-core</artifactId>\n  <version>3.1.1</version>\n  <packaging>jar</packaging>\n</project>\n' > /workspace/local-repo/org/ajoberstar/grgit/grgit-core/3.1.1/grgit-core-3.1.1.pom

RUN mkdir -p /root/.gradle/init.d \
    && printf 'allprojects {\n  buildscript {\n    repositories {\n      maven { url "file:///workspace/local-repo" }\n    }\n  }\n  repositories {\n    maven { url "file:///workspace/local-repo" }\n  }\n}\n' > /root/.gradle/init.d/local-repo.gradle

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/kafka.git && \
    git fetch --depth 30 origin 5928ffd0dc444cf0ef21178e36c1103c3e4a0996 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN gradle :core:testClasses --no-daemon

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

# --- offline-harbor: gradle offline-bake (auto) ---
# Set daemon off + write gradle.properties knobs into GRADLE_USER_HOME (default /root/.gradle).
RUN GUH="${GRADLE_USER_HOME:-/root/.gradle}"; mkdir -p "$GUH"; \
    touch "$GUH/gradle.properties"; \
    grep -q '^org.gradle.daemon=' "$GUH/gradle.properties" || printf 'org.gradle.daemon=false\n' >> "$GUH/gradle.properties"
# Warm ~/.gradle by running the EXACT test command at build time (network ON here).
RUN timeout 3500 bash -c '(gradle :core:test --tests "kafka.coordinator.group.GroupCoordinatorTest" --no-daemon -x rat -x spotbugsMain -x spotbugsTest -x checkstyleMain -x checkstyleTest); cat /workspace/repo/core/build/test-results/test/TEST-*.xml 2>/dev/null || true' > /tmp/gradle_warm.log 2>&1 || true; tail -n 20 /tmp/gradle_warm.log 2>/dev/null || true
# Broader fallback warm in case the exact command's project layout differs.
RUN cd /workspace/repo 2>/dev/null && ( ./gradlew --no-daemon testClasses > /tmp/gradle_warm2.log 2>&1 \
      || ./gradlew --no-daemon compileTestJava >> /tmp/gradle_warm2.log 2>&1 \
      || ./gradlew --no-daemon build -x test >> /tmp/gradle_warm2.log 2>&1 \
      || gradle --no-daemon testClasses >> /tmp/gradle_warm2.log 2>&1 || true ); \
    tail -n 10 /tmp/gradle_warm2.log 2>/dev/null || true
# CRITICAL: remove stale test-results/reports produced by the build-time warm so the
# grader cannot read pre-baked passing XML (would otherwise leak nop reward). The
# downloaded dependency cache lives in $GRADLE_USER_HOME and is untouched.
RUN find /workspace/repo -type d \( -name 'test-results' -o -name 'reports' \) -path '*/build/*' -prune -exec rm -rf {} + 2>/dev/null || true
# Prefetch golden/test-introduced gradle deps (+transitive closure) into the cache.
RUN set -e; mkdir -p /tmp/depwarm; \
    printf 'rootProject.name="depwarm"\n' > /tmp/depwarm/settings.gradle; \
    { printf 'buildscript {\n  repositories { mavenCentral(); google(); maven { url \042https://plugins.gradle.org/m2/\042 }; gradlePluginPortal() }\n  dependencies {\n'; \
      printf '    classpath \042com.diffplug.spotless:spotless-plugin-gradle:3.23.0\042\n    classpath \042com.github.ben-manes:gradle-versions-plugin:0.21.0\042\n    classpath \042com.github.jengelman.gradle.plugins:shadow:4.0.4\042\n    classpath \042gradle.plugin.com.github.spotbugs:spotbugs-gradle-plugin:1.6.9\042\n    classpath \042org.ajoberstar.grgit:grgit-core:3.1.1\042\n    classpath \042org.owasp:dependency-check-gradle:4.0.2\042\n    classpath \042org.scoverage:gradle-scoverage:2.5.0\042\n'; \
      printf '  }\n}\nrepositories {\n  mavenCentral()\n  google()\n  maven { url \042https://maven-central.storage-download.googleapis.com/repos/central/data/\042 }\n  maven { url \042https://plugins.gradle.org/m2/\042 }\n  gradlePluginPortal()\n}\n'; \
      printf 'task resolveWarm { doLast { [\042com.diffplug.spotless:spotless-plugin-gradle:3.23.0\042, \042com.github.ben-manes:gradle-versions-plugin:0.21.0\042, \042com.github.jengelman.gradle.plugins:shadow:4.0.4\042, \042gradle.plugin.com.github.spotbugs:spotbugs-gradle-plugin:1.6.9\042, \042org.ajoberstar.grgit:grgit-core:3.1.1\042, \042org.owasp:dependency-check-gradle:4.0.2\042, \042org.scoverage:gradle-scoverage:2.5.0\042].each { gav -> try { def d = dependencies.create(gav); def c = configurations.detachedConfiguration(d); c.transitive = true; c.resolve() } catch(e) { logger.lifecycle(\042warm-skip \042 + gav) } } } }\n'; \
    } > /tmp/depwarm/build.gradle; \
    ( cd /tmp/depwarm && ( gradle --no-daemon resolveWarm > /tmp/depwarm.log 2>&1 \
        || /workspace/repo/gradlew --no-daemon -p /tmp/depwarm resolveWarm >> /tmp/depwarm.log 2>&1 \
        || true ) ); tail -n 20 /tmp/depwarm.log 2>/dev/null || true; true
# Force gradle OFFLINE at test time via an init script (handles custom GRADLE_USER_HOME).
RUN GUH="${GRADLE_USER_HOME:-/root/.gradle}"; mkdir -p "$GUH/init.d"; \
    printf 'gradle.startParameter.offline = true\n' > "$GUH/init.d/zzz-offline.gradle"

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
