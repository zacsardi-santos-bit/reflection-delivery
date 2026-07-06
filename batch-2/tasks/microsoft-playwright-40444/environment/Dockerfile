FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/microsoft/playwright.git && \
    git fetch --depth 30 --no-tags origin 05deb5d44937d92cef7a99b4cbecb40f1c275dcf && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
WORKDIR /workspace/repo

RUN npm ci

RUN npm run build

RUN cd tests/playwright-test/stable-test-runner && npm install

RUN echo "aW1wb3J0IGpzb24sIHN5cwoKZGVmIHdhbGsoc3VpdGUsIGFuY2VzdG9ycywgdGVzdHMpOgogICAgZm9yIHMgaW4gc3VpdGUuZ2V0KCJzdWl0ZXMiLCBbXSk6CiAgICAgICAgd2FsayhzLCBhbmNlc3RvcnMgKyBbcy5nZXQoInRpdGxlIiwgIiIpXSwgdGVzdHMpCiAgICBmb3Igc3BlYyBpbiBzdWl0ZS5nZXQoInNwZWNzIiwgW10pOgogICAgICAgIHRpdGxlID0gc3BlYy5nZXQoInRpdGxlIiwgIiIpCiAgICAgICAgb2sgPSBzcGVjLmdldCgib2siLCBUcnVlKQogICAgICAgIHN0YXR1cyA9ICJwYXNzZWQiIGlmIG9rIGVsc2UgImZhaWxlZCIKICAgICAgICBmdWxsID0gIiA+ICIuam9pbihhbmNlc3RvcnMgKyBbdGl0bGVdKSBpZiBhbmNlc3RvcnMgZWxzZSB0aXRsZQogICAgICAgIHRlc3RzLmFwcGVuZCh7ImFuY2VzdG9yVGl0bGVzIjogYW5jZXN0b3JzLCAidGl0bGUiOiB0aXRsZSwgImZ1bGxOYW1lIjogZnVsbCwgInN0YXR1cyI6IHN0YXR1c30pCgp3aXRoIG9wZW4oc3lzLmFyZ3ZbMV0pIGFzIGY6CiAgICBkID0ganNvbi5sb2FkKGYpCgpmcCA9ICIvd29ya3NwYWNlL3JlcG8vdGVzdHMvcGxheXdyaWdodC10ZXN0L3JlcG9ydGVyLnNwZWMudHMiCnRlc3RzID0gW10KZm9yIHMgaW4gZC5nZXQoInN1aXRlcyIsIFtdKToKICAgIHdhbGsocywgW10sIHRlc3RzKQoKcCA9IHN1bSgxIGZvciB0IGluIHRlc3RzIGlmIHRbInN0YXR1cyJdID09ICJwYXNzZWQiKQpmID0gc3VtKDEgZm9yIHQgaW4gdGVzdHMgaWYgdFsic3RhdHVzIl0gPT0gImZhaWxlZCIpCnJlc3VsdCA9IHsKICAgICJudW1GYWlsZWRUZXN0cyI6IGYsCiAgICAibnVtUGFzc2VkVGVzdHMiOiBwLAogICAgIm51bVRvdGFsVGVzdHMiOiBwICsgZiwKICAgICJ0ZXN0UmVzdWx0cyI6IFt7Im5hbWUiOiBmcCwgInN0YXR1cyI6ICJwYXNzZWQiIGlmIGYgPT0gMCBlbHNlICJmYWlsZWQiLCAiYXNzZXJ0aW9uUmVzdWx0cyI6IHRlc3RzfV0KfQpwcmludChqc29uLmR1bXBzKHJlc3VsdCkpCg==" | base64 -d > /usr/local/bin/pw2jest.py

RUN echo "IyEvYmluL2Jhc2gKT1VUUFVUX0ZJTEU9IiIKQVJHUz0oKQp3aGlsZSBbWyAkIyAtZ3QgMCBdXTsgZG8KICAgIGNhc2UgJDEgaW4KICAgICAgICAtLWpzb24pIHNoaWZ0IDs7CiAgICAgICAgLS1vdXRwdXRGaWxlPSopIE9VVFBVVF9GSUxFPSIkezEjLS1vdXRwdXRGaWxlPX0iOyBzaGlmdCA7OwogICAgICAgICopIEFSR1MrPSgiJDEiKTsgc2hpZnQgOzsKICAgIGVzYWMKZG9uZQpQV19DTEk9L3dvcmtzcGFjZS9yZXBvL3Rlc3RzL3BsYXl3cmlnaHQtdGVzdC9zdGFibGUtdGVzdC1ydW5uZXIvbm9kZV9tb2R1bGVzL0BwbGF5d3JpZ2h0L3Rlc3QvY2xpCm5vZGUgIiRQV19DTEkiIHRlc3QgIiR7QVJHU1tAXX0iIC0tcmVwb3J0ZXI9anNvbiA+IC90bXAvcHcuanNvbiAyPi9kZXYvbnVsbApQV19FWElUPSQ/CkpFU1RfSlNPTj0kKHB5dGhvbjMgL3Vzci9sb2NhbC9iaW4vcHcyamVzdC5weSAvdG1wL3B3Lmpzb24gMj4vZGV2L251bGwpCmlmIFsgLXogIiRKRVNUX0pTT04iIF07IHRoZW4KICAgIEpFU1RfSlNPTj0neyJudW1GYWlsZWRUZXN0cyI6MSwibnVtUGFzc2VkVGVzdHMiOjAsIm51bVRvdGFsVGVzdHMiOjEsInRlc3RSZXN1bHRzIjpbeyJuYW1lIjoicmVwb3J0ZXIuc3BlYy50cyIsInN0YXR1cyI6ImZhaWxlZCIsImFzc2VydGlvblJlc3VsdHMiOltdfV19JwpmaQppZiBbIC1uICIkT1VUUFVUX0ZJTEUiIF07IHRoZW4KICAgIG1rZGlyIC1wICIkKGRpcm5hbWUgIiRPVVRQVVRfRklMRSIpIgogICAgcHJpbnRmICclcwonICIkSkVTVF9KU09OIiA+ICIkT1VUUFVUX0ZJTEUiCmVsc2UKICAgIHByaW50ZiAnJXMKJyAiJEpFU1RfSlNPTiIKZmkKZXhpdCAkUFdfRVhJVAo=" | base64 -d > /usr/local/bin/playwright-run && chmod +x /usr/local/bin/playwright-run


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
