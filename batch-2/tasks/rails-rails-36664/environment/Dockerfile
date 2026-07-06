FROM ruby:2.7-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libsqlite3-dev sqlite3 \
    default-libmysqlclient-dev \
    libpq-dev \
    libxml2-dev libxslt1-dev \
    libncurses5-dev \
    pkg-config zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

RUN gem install bundler -v 2.1.2

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/rails/rails.git && \
    git fetch --depth 30 origin 00b277bda0285d4f30146b00554605518b27c2db && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN bundle config set --local without 'doc job cable storage ujs' && \
    bundle update mimemagic 2>&1 | tail -20 && \
    bundle install --jobs 4 2>&1 | tail -30 && \
    cp Gemfile.lock /workspace/Gemfile.lock.fixed

RUN mkdir -p /workspace/test-reports

RUN printf "require 'minitest/reporters'\nMinitest::Reporters.use!(Minitest::Reporters::JUnitReporter.new('/workspace/test-reports'))\n" > /workspace/minitest_junit_setup.rb

WORKDIR /workspace

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# OFFLINE-GRADLE-BAKE
RUN cd /workspace/repo && (chmod +x ./gradlew 2>/dev/null; ./gradlew --no-daemon testClasses 2>/dev/null || ./gradlew --no-daemon compileTestJava 2>/dev/null || ./gradlew --no-daemon assemble 2>/dev/null || true)
RUN GUH="${GRADLE_USER_HOME:-$HOME/.gradle}" && mkdir -p "$GUH/init.d" && printf 'gradle.startParameter.offline = true\n' > "$GUH/init.d/zzz-offline.gradle"

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
