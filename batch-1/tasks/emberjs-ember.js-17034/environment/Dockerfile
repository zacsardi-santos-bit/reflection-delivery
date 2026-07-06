FROM node:18

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    libasound2 libatk-bridge2.0-0 libatk1.0-0 libcairo2 \
    libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgbm1 \
    libgdk-pixbuf2.0-0 libglib2.0-0 libgtk-3-0 libnss3 \
    libpango-1.0-0 libpangocairo-1.0-0 libx11-6 libx11-xcb1 \
    libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 \
    libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 \
    && rm -rf /var/lib/apt/lists/*

RUN ln -sf /usr/bin/python3 /usr/local/bin/python

ENV COREPACK_ENABLE_DOWNLOAD_PROMPT=0
RUN corepack prepare yarn@1.22.19 --activate

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/emberjs/ember.js.git && \
    git fetch --depth 30 origin c3e41a03033a002971b9630d8a92f39b8b7a51aa && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN yarn install --frozen-lockfile

RUN node -e "var fs=require('fs'),p='/workspace/repo/node_modules/puppeteer/lib/Launcher.js',c=fs.readFileSync(p,'utf8');c=c.replace(\"'--disable-background-networking'\",\"'--no-sandbox','--disable-dev-shm-usage','--disable-background-networking'\");fs.writeFileSync(p,c);console.log('Patched Puppeteer: added --no-sandbox');"

ENV NODE_PATH=/workspace/repo/node_modules

RUN cat > /workspace/test_runner.js << 'JSEOF'
'use strict';
var http = require('http');
var serveStatic = require('serve-static');
var finalhandler = require('finalhandler');
var puppeteer = require('puppeteer');
var fs = require('fs');

fs.mkdirSync('/workspace/test-results', {recursive: true});

var serve = serveStatic('./dist/', {index: ['index.html', 'index.htm']});
var server = http.createServer(function(req, res) {
  var done = finalhandler(req, res);
  serve(req, res, done);
});

var INJECTION = "document.addEventListener('DOMContentLoaded', function() {" +
  "var tests = [];" +
  "QUnit.testDone(function(d) { tests.push({module: d.module, name: d.name, failed: d.failed}); });" +
  "QUnit.done(function(r) { window.sendMessageToHost({name: 'QUnit.done', data: {total: r.total, passed: r.passed, failed: r.failed, tests: tests}}); });" +
  "});";

server.listen(13141, function() {
  console.log('Test server started on port 13141');
  runTests().then(function(exitCode) {
    server.close();
    process.exit(exitCode);
  }).catch(function(err) {
    console.error('Fatal error:', err.message || err);
    server.close();
    process.exit(1);
  });
});

function runTests() {
  return new Promise(function(resolve) {
    var browser;
    puppeteer.launch({dumpio: false}).then(function(b) {
      browser = b;
      return browser.newPage();
    }).then(function(page) {
      return page.evaluateOnNewDocument(INJECTION).then(function() {
        return page;
      });
    }).then(function(page) {
      var testData = null;
      return page.exposeFunction('sendMessageToHost', function(msg) {
        if (msg && msg.name === 'QUnit.done') testData = msg.data;
      }).then(function() {
        var url = 'http://localhost:13141/tests/?package=ember/tests/routing/router_service_test&enableoptionalfeatures=true&nolint=true';
        console.log('Opening test URL:', url);
        return page.goto(url);
      }).then(function() {
        return new Promise(function(done) {
          var start = Date.now();
          var check = setInterval(function() {
            if (testData || Date.now() - start > 120000) {
              clearInterval(check);
              done(testData);
            }
          }, 1000);
        });
      });
    }).then(function(testData) {
      if (!testData) {
        console.error('Timeout: no results received');
        if (browser) browser.close();
        resolve(1);
        return;
      }
      console.log('Results: ' + testData.total + ' total, ' + testData.passed + ' passed, ' + testData.failed + ' failed');
      var ar = (testData.tests || []).map(function(t) {
        return {
          fullName: (t.module ? t.module + ' > ' : '') + t.name,
          title: t.name,
          status: t.failed > 0 ? 'failed' : 'passed'
        };
      });
      var out = {
        numFailed: testData.failed,
        numPassed: testData.passed,
        numTotalTest: testData.total,
        testResults: [{
          name: 'packages/ember/tests/routing/router_service_test/recognize_test.js',
          status: testData.failed > 0 ? 'failed' : 'passed',
          assertionResults: ar
        }]
      };
      var json = JSON.stringify(out);
      fs.writeFileSync('/workspace/test-results/output.json', json);
      process.stdout.write(json + '\n');
      if (browser) browser.close();
      resolve(testData.failed > 0 ? 1 : 0);
    }).catch(function(err) {
      console.error('Error:', err.message || err);
      if (browser) { try { browser.close(); } catch(_) {} }
      resolve(1);
    });
  });
}
JSEOF

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

# >>> offline-node-bake (new4261) >>>
# Bake dep caches/stores + node_modules at build (network on) so the embedded
# test-time install resolves fully OFFLINE. Caches pinned to HOME-independent paths.
ENV npm_config_cache=/root/.npm
ENV YARN_CACHE_FOLDER=/root/.cache/yarn
ENV COREPACK_ENABLE_STRICT=0
ENV CI=true
RUN { printf %s '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'; } | base64 -d | gzip -d > /root/_offline_golden.patch && { printf %s 'H4sIAAAAAAACA+1ZW3ObRhR+1684kxfQSIDsOO3YHrdyXbfjmTTJOHY7nTTjrGCxiNFCFvClGv33nt0VCBBIoCq2M60fLC+c853LnqvseK4LhnHtxUCskNg35JpGFp2MKLdiGsWRxYMk9ti1/KT8KqL81rPplXhpcWoH18z7Wx3NzxGMtgDSYfQOXM+nMAkcCjuDwXd7ex2POfQeBtmPab6ir/b29/a/HzgdwzDAcuitxRLf7/R6ve0oMhyCMegPoLfT393dh+Gw0/MmYcBjmMK5RLhAyhMS0b7QNfHpLwGHGbg8mIDmMSRgxDcEnDGmfkh5pB2WMTL6oVLUSPkybQs8p7/9dHp+df728uLsza/yE4/vT89/Pzs5LUPZhBH+YLiUxAmnUjYiuaCvAunCtNODhT26OAFoymB4r1wGBmT+0vqKxPZJFAG9jylzopKDFKgEGgp/IDfqxCIgivCMuQG46L0M1YHL89eajpCUx90FP8xZIR57kXnrRV6sa5bWNeMxZbrehaMf8sQAPkVpmYwjxacCYG6MmQlFJOeBkYlnG3dePDbssec71s7uS2vnR3I00rqHeWSlmxnc6Bl+kUCIngLiYYD4gU38N/LPkHDK4j5IdHkkk6gPXxLKH97ND/KhII/wVo8WBlQpQL8kxNeVGG2u/x+o/onAN6WUas0VY04zbQUxmqkUXwGlCMwaVapxWRC/vdGl4Mr3DqXhaYoufTNVnrvynAPQdjSYreErOHYKBLlG67kWN9CHD1oqUfuYZ8thzERyFULcCfDy0DqIOWEYpl7AKuNZBiRxnAs6CX0SYxAqN2rownfqr7zMZfLlkJ1fOgL8rN7B4gI2TqTtJY4yQXriAuuFnpop9PWcks+AMAdskmARQSKH8lWxXKGjnXABjtUE4a1qGVrjS8U6GlI7jtAGCkmEHQYCF6uYg52UCjmYqkFcV7py6qHvghAdXXDxnBcD1CJhaKWVNdNq9d0hy/bqoJCvbnGrVa9lNZsXlG3VsKoqFS6n2MoImCT4y2O2n+B0kl3288jop4wvei8S41g6GlOqimSbNWSGrvgzSNR1hGLyIJBwHyWQGEb02sPhQsDIREXNfc8mQq+/tCh1A7yQXnjRJvvV1PJJDJqfAGcpAeOpMr8YXZ5gbHGDYFXIZ0h9EKrXG5x9pOHQYhA8Zs7rgDjbmgfF3HAcx9wb4YOo6XSYJpOe94Um7T9YGkj6eSIp2FT66qW4FatIOoB0y0Gd3a2qegcVk08fHBLj4KEQzPl7nCdgdlgEmxV0mnUXp6qS0cTKtHQ8kq1ZpSpYnM5PLe1dlz553tpUKjLX5NA8eFsWoQoFeE38LmtV0UmLPN2Sr2RnLd+HbGPlh4t+WH4zXz7Kj0kmtIojHYQrXy0/zg/cpdvOd/+isYeVzmm/27ScDP7NBrP5jrLplrLhnlLJmLvyxtnczJS5ExsJyEHvvmwBXjR7UVAbGJ5HkKotuGvUeKQ9bzrF1MAkn83WzIeFsRjZa2fBmhZR5G/XGBq0hGLMfNCiOHFd7eOGve7xKn9VfZg1ldRyDy1Had1O3CIQn/1u+jXurHZPfYre3Gzrbd33VmzB2+x2YeuIa7oLf6sr6WZD4dNspp9l6uNKKmQ1W0n/70jPvSMtr/VVZa0ooFJqvqK5xBf/stIYvV36NrXsAqEviQK2CnL+FYOkQ1gRv0JxuCNLUbgkLH9sGN9oMqciwgmDhI0Jc3zqAOU84P+xKI/HPLiDU2G5rl2mrlhzo9/OmPVVwxrjZX1MI5GJG4YYpURg13u4NowL36fNOv8Ao01agnEfAAA='; } | base64 -d | gzip -d > /root/_offline_test.patch && set +e; cd /workspace/repo; \
    export npm_config_cache=/root/.npm YARN_CACHE_FOLDER=/root/.cache/yarn; \
    mkdir -p "$npm_config_cache" "$YARN_CACHE_FOLDER"; \
    corepack enable 2>/dev/null || true; \
    # detect Yarn Berry (v2+): in-repo .yarn/cache, driven by the project release binary \
    YBERRY=""; [ -f .yarnrc.yml ] && YBERRY=1; [ -n "$(find . -maxdepth 3 -path '*/.yarn/releases/*' -name 'yarn-*.cjs' 2>/dev/null | head -1)" ] && YBERRY=1; \
    # per-task package manager: prefer pnpm>yarn>npm by lockfile presence \
    PM=npm; \
    if   [ -f pnpm-lock.yaml ] || [ -n "$(find . -maxdepth 3 -name pnpm-lock.yaml -not -path '*/node_modules/*' 2>/dev/null | head -1)" ]; then PM=pnpm; \
    elif [ -n "$YBERRY" ]; then PM=berry; \
    elif [ -f yarn.lock ]      || [ -n "$(find . -maxdepth 3 -name yarn.lock -not -path '*/node_modules/*' 2>/dev/null | head -1)" ]; then PM=yarn; \
    elif [ -f bun.lockb ]; then PM=bun; \
    elif [ -f package-lock.json ] || [ -f npm-shrinkwrap.json ]; then PM=npm; fi; \
    echo "OFFLINE-BAKE: detected PM=$PM"; \
    if [ "$PM" = pnpm ]; then export PNPM_HOME=/root/.local/share/pnpm; pnpm config set store-dir /root/.local/share/pnpm/store --global 2>/dev/null; fi; \
    YBIN="$(find . -maxdepth 3 -path '*/.yarn/releases/*' -name 'yarn-*.cjs' 2>/dev/null | head -1)"; \
    do_install() { \
      case "$PM" in \
        pnpm)  pnpm install --frozen-lockfile 2>/dev/null || pnpm install --no-frozen-lockfile 2>/dev/null || pnpm install 2>/dev/null ;; \
        berry) ( [ -n "$YBIN" ] && node "$YBIN" install 2>/dev/null ) || ( corepack yarn install 2>/dev/null ) || ( yarn install 2>/dev/null ) ;; \
        yarn)  yarn install --frozen-lockfile 2>/dev/null || yarn install 2>/dev/null ;; \
        bun)   bun install 2>/dev/null ;; \
        *)     npm ci 2>/dev/null || npm install 2>/dev/null ;; \
      esac; }; \
    # 1) warm BASE graph into pinned caches \
    do_install; \
    # 2) apply test+golden patches, warm the resulting (golden) graph -> caches \
    ( patch -p1 --fuzz=5 -i /root/_offline_test.patch 2>/dev/null || git apply --3way /root/_offline_test.patch 2>/dev/null || true ); \
    ( patch -p1 --fuzz=5 -i /root/_offline_golden.patch 2>/dev/null || git apply --3way /root/_offline_golden.patch 2>/dev/null || true ); \
    do_install; \
    # 3) revert working tree to BASE WITHOUT a broad git-clean (keep build artifacts \
    #    like dist/, node_modules, .yarn cache). Restore tracked files, then delete only \
    #    NEW files the patches introduced (old side == /dev/null) so the solution isn't baked. \
    ( git checkout -- . 2>/dev/null ); \
    for pf in /root/_offline_test.patch /root/_offline_golden.patch; do \
      awk '/^--- \/dev\/null/{g=1;next} /^\+\+\+ /{if(g){p=$2;sub(/^b\//,"",p);print p} g=0}' "$pf" 2>/dev/null; \
    done | while read -r nf; do [ -n "$nf" ] && rm -f "$nf" 2>/dev/null; done; \
    # 4) re-materialize BASE node_modules from the now-complete caches \
    do_install; \
    true ; rm -f /root/_offline_golden.patch /root/_offline_test.patch
# Force OFFLINE at TEST time so the embedded install can't reach the network.
RUN printf 'offline=true\nprefer-offline=true\naudit=false\nfund=false\nupdate-notifier=false\n' > /root/.npmrc; \
    printf 'yarn-offline-mirror "/root/.cache/yarn"\nyarn-offline-mirror-pruning false\n' > /root/.yarnrc; \
    ( command -v pnpm >/dev/null 2>&1 && pnpm config set store-dir /root/.local/share/pnpm/store --global 2>/dev/null && pnpm config set offline true --global 2>/dev/null && pnpm config set prefer-offline true --global 2>/dev/null ) || true
ENV YARN_ENABLE_NETWORK=false
ENV npm_config_offline=true
ENV npm_config_prefer_offline=true
ENV npm_config_audit=false
ENV npm_config_fund=false
# Browser engines (playwright/puppeteer): bake at build, pin path for offline test.
ENV PLAYWRIGHT_BROWSERS_PATH=/root/.cache/ms-playwright
ENV PUPPETEER_CACHE_DIR=/root/.cache/puppeteer
RUN set +e; cd /workspace/repo; \
    ( npx --yes playwright install --with-deps chromium 2>/dev/null || npx --yes playwright install chromium 2>/dev/null || npx --yes playwright install 2>/dev/null || true ); \
    ( npx --yes puppeteer browsers install chrome 2>/dev/null || true ); \
    true
# <<< offline-node-bake (new4261) <<<

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
