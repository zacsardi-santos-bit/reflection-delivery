FROM ruby:3.3-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libxml2-dev libxslt1-dev zlib1g-dev libyaml-dev \
    libargon2-dev pkg-config \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/rails/rails.git && \
    git fetch --depth 30 --no-tags origin 5b4cf30c08a2dd022ed8c56b2958aff7c81ca4b6 && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
RUN cat > /workspace/test_runner.rb << 'RUBY_HEREDOC'
require "minitest"

class JUnitReporter < Minitest::AbstractReporter
  def initialize
    @results = []
  end

  def record(r)
    @results << r
  end

  def report
    puts '<?xml version="1.0"?>'
    puts "<testsuite tests=\"#{@results.size}\">"
    @results.each do |r|
      klass = r.class.name
      name = r.name.gsub('"', '&quot;')
      puts "  <testcase classname=\"#{klass}\" name=\"#{name}\">"
      begin
        if r.skipped?
          puts "    <skipped/>"
        elsif !r.passed?
          failure = r.failures.first
          msg = failure ? failure.message.to_s[0, 500].gsub('&', '&amp;').gsub('<', '&lt;').gsub('>', '&gt;').gsub('"', '&quot;') : "error"
          puts "    <failure>#{msg}</failure>"
        end
      rescue => e
        puts "    <failure>#{e.message[0, 200].gsub('<', '&lt;')}</failure>"
      end
      puts "  </testcase>"
    end
    puts "</testsuite>"
  end
end

Minitest.extensions << "junit_inline"
module Minitest
  def self.plugin_junit_inline_init(options)
    Minitest.reporter << JUnitReporter.new
  end
end

$LOAD_PATH.unshift("/workspace/repo/actionview/test")

load "/workspace/repo/actionview/test/template/cache_expiry_test.rb"
begin
  load "/workspace/repo/railties/test/application/reloaders_collection_test.rb"
rescue LoadError => e
  $stderr.puts "Note: #{e.message}"
end
RUBY_HEREDOC

WORKDIR /workspace/repo

ENV BUNDLE_WITHOUT="job:cable:storage:doc:mdl:rubocop:db:test"

RUN bundle install 2>&1 | tail -20


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true


# offline login-shell PATH bake
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh

# offline-harbor: git blob-drop shrink (client-contract .git<100MB, HEAD==base)
RUN set +e; cd /workspace/repo 2>/dev/null && ( rm -rf .git/modules .git/worktrees 2>/dev/null; git reflog expire --expire=now --all 2>/dev/null; rm -rf .git/logs; git remote remove origin 2>/dev/null; git tag -l | xargs -r git tag -d >/dev/null 2>&1; git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d 2>/dev/null; git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_keep.txt; if [ -s /tmp/_keep.txt ]; then git pack-objects --quiet /tmp/_kp < /tmp/_keep.txt >/dev/null 2>&1 && rm -rf .git/objects/pack && mkdir -p .git/objects/pack && find .git/objects -maxdepth 1 -type d -name '[0-9a-f][0-9a-f]' -exec rm -rf {} + 2>/dev/null; mv /tmp/_kp-*.pack /tmp/_kp-*.idx .git/objects/pack/ 2>/dev/null; fi; git rev-parse HEAD >/dev/null 2>&1 || echo "WARN head-broken"; du -sm .git 2>/dev/null | cut -f1 ) ; true
