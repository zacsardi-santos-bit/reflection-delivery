FROM ruby:3.3

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y -q \
    build-essential autoconf libtool clang python3 python3-pip ccache \
    && rm -rf /var/lib/apt/lists/*

RUN pip3 install compiledb --break-system-packages || pip3 install compiledb

RUN gem install bundler --no-doc

ENV LC_ALL=C.UTF-8
ENV CC=clang
ENV CXX=clang++

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/natalie-lang/natalie.git && \
    git fetch --depth 30 origin a1dbb3b1da42a9d28de16b83e72af0f144e4ec65 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN git submodule update --init --recursive

RUN bundle install

RUN SKIP_SUBMODULE_UPDATE=1 rake build_release

RUN printf '%s\n' '#!/usr/bin/env ruby' \
    'lines = STDIN.read.split("\n")' \
    'lines.each do |line|' \
    '  next if line =~ /^Removing /' \
    '  next if line =~ /^Updated \d+ paths/' \
    '  if line =~ /^- \[OK\]\s+(.+)$/' \
    '    puts "✓ #{$1}"' \
    '  elsif line =~ /^- \[Fail\]\s+(.+)$/' \
    '    puts "✗ #{$1}"' \
    '  end' \
    'end' \
    > /workspace/nat_to_rspec.rb && chmod +x /workspace/nat_to_rspec.rb

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
