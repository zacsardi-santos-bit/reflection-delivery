FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/demisto/content.git && \
    git fetch --depth 1 origin 3a43e13926cf90882da83ef248d90c71d667c462 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

WORKDIR /workspace/repo

RUN sed -i 's/\r$//' Packs/XQLDSHelper/Scripts/XQLDSHelper/README.md \
                     Packs/XQLDSHelper/Scripts/XQLDSHelper/XQLDSHelper_test.py && \
    git config user.email "bot@auto.com" && \
    git config user.name "Bot" && \
    git add Packs/XQLDSHelper/Scripts/XQLDSHelper/README.md \
            Packs/XQLDSHelper/Scripts/XQLDSHelper/XQLDSHelper_test.py && \
    git commit -m "normalize line endings"

RUN pip install pytest requests-mock requests urllib3 dateparser

ENV PYTHONPATH=/workspace/repo/Packs/Base/Scripts/CommonServerPython:/workspace/repo/Tests/demistomock:/workspace/repo/Packs/ApiModules/Scripts/DemistoClassApiModule:/workspace/repo/Packs/Cyberint/Integrations/FeedCyberint

WORKDIR /workspace

# >>> final-git-hygiene (compliance) >>>
RUN cd /workspace/repo 2>/dev/null && ( \
    B=3a43e13926cf90882da83ef248d90c71d667c462; H=$(git rev-parse HEAD 2>/dev/null); \
    if [ "$H" != "$B" ]; then git reset --soft "$B" 2>/dev/null || true; fi; \
    git remote remove origin 2>/dev/null || true; \
    git tag -l 2>/dev/null | xargs -r git tag -d 2>/dev/null || true; \
    git reflog expire --expire=now --all 2>/dev/null || true; \
    git gc --prune=now 2>/dev/null || true; \
    rm -rf .git/logs ) ; true
# <<< final-git-hygiene (compliance) <<<

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
# --- offline size fix: drop redundant blob objects from .git (working tree stays checked out);
# keeps commit+tree objects so HEAD==base, shallow, git-present all hold and .git<100MB ---
RUN cd /workspace/repo && \
    mkdir -p /tmp/np && \
    git rev-list --objects --all 2>/dev/null | awk '{print $1}' | \
      git cat-file --batch-check='%(objectname) %(objecttype)' 2>/dev/null | \
      awk '$2!="blob"{print $1}' | \
      git pack-objects --no-reuse-delta /tmp/np/pack >/tmp/np/h 2>/dev/null && \
    rm -f .git/objects/pack/* && \
    cp /tmp/np/pack-*.pack /tmp/np/pack-*.idx .git/objects/pack/ && \
    find .git/objects -type d -empty -delete 2>/dev/null; \
    git prune-packed 2>/dev/null || true; \
    rm -rf /tmp/np; true

RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
