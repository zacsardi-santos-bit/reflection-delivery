FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    python3 python3-dev python3-pip \
    openjdk-21-jdk \
    gnupg apt-transport-https \
    && rm -rf /var/lib/apt/lists/*

RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.asc] https://packages.cloud.google.com/apt cloud-sdk main" \
    | tee /etc/apt/sources.list.d/google-cloud-sdk.list && \
    curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg \
    | tee /usr/share/keyrings/cloud.google.asc > /dev/null && \
    apt-get update && apt-get install -y \
    google-cloud-cli \
    google-cloud-cli-datastore-emulator \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/google/clusterfuzz.git && \
    git fetch --depth 30 --no-tags origin 7444e54b5a7e5ccf8f945b90d165fa81294b3eed && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
WORKDIR /workspace/repo

RUN pip3 install --break-system-packages \
    "aiohttp==3.10.5" \
    "antlr4-python3-runtime==4.7.2" \
    "bcrypt==4.1.2" \
    "certifi==2024.2.2" \
    "cryptography==37.0.4" \
    "future==0.17.1" \
    "google-api-core==2.11.1" \
    "google-api-python-client==2.93.0" \
    "google-auth==2.22.0" \
    "google-auth-oauthlib==0.4.1" \
    "google-cloud-batch==0.11.0" \
    "google-cloud-core==2.3.3" \
    "google-cloud-datastore==2.16.1" \
    "google-cloud-logging==3.6.0" \
    "google-cloud-monitoring==2.15.1" \
    "google-cloud-ndb==2.2.1" \
    "google-cloud-secret-manager==2.17.0" \
    "google-cloud-storage==2.10.0" \
    "google-crc32c==1.5.0" \
    "grpcio==1.62.2" \
    "httplib2==0.19.0" \
    "Jinja2==3.1.4" \
    "kubernetes==34.1.0" \
    "mozprocess==1.3.1" \
    "oauth2client==4.1.3" \
    "psutil==5.9.4" \
    "protobuf==4.23.4" \
    "pyOpenSSL==22.0.0" \
    "python-dateutil==2.8.1" \
    "PyYAML==6.0.1" \
    "pytz==2023.3" \
    "redis==4.6.0" \
    "requests>=2.28.0" \
    "six" \
    "wrapt==1.16.0" \
    "parameterized==0.9.0" \
    "pytest"

ENV PYTHONPATH=/workspace/repo/src
ENV ROOT_DIR=/workspace/repo
ENV CONFIG_DIR_OVERRIDE=/workspace/repo/configs/test
ENV DATASTORE_USE_PROJECT_ID_AS_APP_ID=true
ENV TEST_BOT_ENVIRONMENT=1


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
