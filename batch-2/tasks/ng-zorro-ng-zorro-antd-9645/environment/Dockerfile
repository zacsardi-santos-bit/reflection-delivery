FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    chromium \
    && rm -rf /var/lib/apt/lists/*

ENV PUPPETEER_SKIP_DOWNLOAD=true
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium
ENV CHROME_BIN=/usr/bin/chromium

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/NG-ZORRO/ng-zorro-antd.git && \
    git fetch --depth 30 --no-tags origin d7fbc9076cdd5026c28029e3644a0883c8f779e4 && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
WORKDIR /workspace/repo

RUN npm ci

RUN echo "IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgeG1sLmV0cmVlLkVsZW1lbnRUcmVlIGFzIEVULCBqc29uLCBnbG9iLCBzeXMsIG9zLCByZQoKb3V0cHV0X2ZpbGUgPSBzeXMuYXJndlsxXSBpZiBsZW4oc3lzLmFyZ3YpID4gMSBlbHNlICIvdG1wL2plc3Qtb3V0cHV0Lmpzb24iCmV4aXRfY29kZSA9IGludChzeXMuYXJndlsyXSkgaWYgbGVuKHN5cy5hcmd2KSA+IDIgZWxzZSAwCgpGSUxURVJfUEFUVEVSTiA9ICJpbnB1dC13cmFwcGVyIHdpdGggY291bnQgY29uZmlnIgoKZmlsZXMgPSBnbG9iLmdsb2IoIi93b3Jrc3BhY2UvcmVwby9qdW5pdC8qLnhtbCIpCnJlc3VsdHMgPSBbXQoKaWYgZmlsZXM6CiAgICBmb3IgZiBpbiBmaWxlczoKICAgICAgICB0cnk6CiAgICAgICAgICAgIHJvb3QgPSBFVC5wYXJzZShmKS5nZXRyb290KCkKICAgICAgICAgICAgdGVzdGNhc2VzID0gcm9vdC5maW5kYWxsKCIuLy90ZXN0Y2FzZSIpCiAgICAgICAgICAgIGZvciB0YyBpbiB0ZXN0Y2FzZXM6CiAgICAgICAgICAgICAgICBuID0gdGMuZ2V0KCJuYW1lIiwgIiIpCiAgICAgICAgICAgICAgICBpZiBGSUxURVJfUEFUVEVSTiBub3QgaW4gbjoKICAgICAgICAgICAgICAgICAgICBjb250aW51ZQogICAgICAgICAgICAgICAgcyA9ICJmYWlsZWQiIGlmIHRjLmZpbmQoImZhaWx1cmUiKSBpcyBub3QgTm9uZSBvciB0Yy5maW5kKCJlcnJvciIpIGlzIG5vdCBOb25lIGVsc2UgInBhc3NlZCIKICAgICAgICAgICAgICAgIHJlc3VsdHMuYXBwZW5kKHsiYW5jZXN0b3JUaXRsZXMiOiBbXSwgInRpdGxlIjogbiwgInN0YXR1cyI6IHMsICJmdWxsTmFtZSI6IG59KQogICAgICAgIGV4Y2VwdCBFeGNlcHRpb246CiAgICAgICAgICAgIHBhc3MKZWxpZiBleGl0X2NvZGUgIT0gMDoKICAgIGZha2VfdGVzdHMgPSBbCiAgICAgICAgImlucHV0LXdyYXBwZXIgd2l0aCBjb3VudCBjb25maWcgc2hvdWxkIGJlIHdvcmsgd2l0aCBzaG93IGNvdW50IHNob3VsZCBiZSBzaG93L2hpZGRlbiBjb3VudCBzdWZmaXgiLAogICAgICAgICJpbnB1dC13cmFwcGVyIHdpdGggY291bnQgY29uZmlnIHNob3VsZCBiZSB3b3JrIHdpdGggc2hvdyBjb3VudCBzaG91bGQgYmUgY29ycmVjdCBjb3VudGluZyIsCiAgICAgICAgImlucHV0LXdyYXBwZXIgd2l0aCBjb3VudCBjb25maWcgc2hvdWxkIGJlIHdvcmsgd2l0aCBjb3VudCAvIG1heCBzaG91bGQgYmUgc2hvdy9oaWRkZW4gY291bnQgc3VmZml4IiwKICAgICAgICAiaW5wdXQtd3JhcHBlciB3aXRoIGNvdW50IGNvbmZpZyBzaG91bGQgYmUgd29yayB3aXRoIGNvdW50IC8gbWF4IHNob3VsZCBiZSBjb3JyZWN0IGNvdW50aW5nIiwKICAgICAgICAiaW5wdXQtd3JhcHBlciB3aXRoIGNvdW50IGNvbmZpZyBzaG91bGQgYmUgd29yayB3aXRoIGNvdW50IC8gbWF4IC8gc3RyYXRlZ3kgLyBmb3JtYXR0ZXIgc2hvdWxkIGJlIHNob3cvaGlkZGVuIGNvdW50IHN1ZmZpeCIsCiAgICAgICAgImlucHV0LXdyYXBwZXIgd2l0aCBjb3VudCBjb25maWcgc2hvdWxkIGJlIHdvcmsgd2l0aCBjb3VudCAvIG1heCAvIHN0cmF0ZWd5IC8gZm9ybWF0dGVyIHNob3VsZCBiZSBjb3JyZWN0IGNvdW50aW5nIiwKICAgICAgICAiaW5wdXQtd3JhcHBlciB3aXRoIGNvdW50IGNvbmZpZyBzaG91bGQgYmUgd29yayB3aXRoIGNvdW50IC8gbWF4IC8gc3RyYXRlZ3kgLyBmb3JtYXR0ZXIgc2hvdWxkIGJlIHdvcmsgY291bnQgc3RyYXRlZ3kiLAogICAgICAgICJpbnB1dC13cmFwcGVyIHdpdGggY291bnQgY29uZmlnIHNob3VsZCBiZSB3b3JrIHdpdGggY291bnQgLyBtYXggLyBzdHJhdGVneSAvIGZvcm1hdHRlciBzaG91bGQgYmUgd29yayBleGNlZWRGb3JtYXR0ZXIiLAogICAgXQogICAgZm9yIGZuIGluIGZha2VfdGVzdHM6CiAgICAgICAgcmVzdWx0cy5hcHBlbmQoeyJhbmNlc3RvclRpdGxlcyI6IFtdLCAidGl0bGUiOiBmbiwgInN0YXR1cyI6ICJmYWlsZWQiLCAiZnVsbE5hbWUiOiBmbn0pCgpucCA9IHN1bSgxIGZvciByIGluIHJlc3VsdHMgaWYgclsic3RhdHVzIl0gPT0gInBhc3NlZCIpCm5mID0gc3VtKDEgZm9yIHIgaW4gcmVzdWx0cyBpZiByWyJzdGF0dXMiXSA9PSAiZmFpbGVkIikKb3V0ID0gewogICAgInRlc3RSZXN1bHRzIjogW3sKICAgICAgICAibmFtZSI6ICIvd29ya3NwYWNlL3JlcG8vY29tcG9uZW50cy9pbnB1dC9pbnB1dC13cmFwcGVyLmNvbXBvbmVudC5zcGVjLnRzIiwKICAgICAgICAiYXNzZXJ0aW9uUmVzdWx0cyI6IHJlc3VsdHMsCiAgICAgICAgIm51bVBhc3NpbmdUZXN0cyI6IG5wLAogICAgICAgICJudW1GYWlsaW5nVGVzdHMiOiBuZiwKICAgICAgICAic3RhdHVzIjogImZhaWxlZCIgaWYgbmYgPiAwIGVsc2UgInBhc3NlZCIKICAgIH1dLAogICAgIm51bVBhc3NlZFRlc3RTdWl0ZXMiOiAwIGlmIG5mID4gMCBlbHNlIDEsCiAgICAibnVtRmFpbGVkVGVzdFN1aXRlcyI6IDEgaWYgbmYgPiAwIGVsc2UgMCwKICAgICJudW1QYXNzZWRUZXN0cyI6IG5wLAogICAgIm51bUZhaWxlZFRlc3RzIjogbmYKfQp3aXRoIG9wZW4ob3V0cHV0X2ZpbGUsICJ3IikgYXMgZmg6CiAgICBqc29uLmR1bXAob3V0LCBmaCkK" | base64 -d > /workspace/karma2jest.py && chmod +x /workspace/karma2jest.py


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
