FROM python:3.12

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libpq-dev \
    postgresql postgresql-client \
    && rm -rf /var/lib/apt/lists/*

RUN pip install uv

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/saleor/saleor.git && \
    git fetch --depth 30 --no-tags origin c9ed53737fe20e9b7e638100c19a48b1f2ccc50f && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
WORKDIR /workspace/repo

RUN uv sync

ENV PATH="/workspace/repo/.venv/bin:$PATH"
ENV PYTHONPATH="/workspace/repo"
ENV DATABASE_URL="postgres://saleor:saleor@localhost:5432/saleor"
ENV SECRET_KEY="ci-test"
ENV CACHE_URL="locmem://"

RUN printf "CREATE USER saleor WITH PASSWORD 'saleor' CREATEDB;\n" > /tmp/create_user.sql \
    && printf "CREATE DATABASE saleor OWNER saleor;\n" > /tmp/create_db.sql \
    && service postgresql start \
    && sleep 5 \
    && su -s /bin/bash postgres -c "psql postgres -f /tmp/create_user.sql" \
    && su -s /bin/bash postgres -c "psql postgres -f /tmp/create_db.sql" \
    && service postgresql stop


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

CMD tail -f /dev/null

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
