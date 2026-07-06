FROM python:3.8

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    postgresql postgresql-contrib libpq-dev redis-server \
    && rm -rf /var/lib/apt/lists/*

RUN find /etc/postgresql -name pg_hba.conf -exec sed -i -e 's/scram-sha-256/trust/g' -e 's/md5/trust/g' {} \; 2>/dev/null || true

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/netbox-community/netbox.git && \
    git fetch --depth 30 origin 7f5571200c5deeb0d805fe8bee80af210f70a399 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --no-cache-dir "MarkupSafe<2.0" 2>&1 | tail -3

RUN pip install --no-cache-dir -r requirements.txt 2>&1 | tail -5

RUN pip install --no-cache-dir pytest pytest-django 2>&1 | tail -5

RUN cp netbox/netbox/configuration.example.py netbox/netbox/configuration.py && \
    sed -i "s/ALLOWED_HOSTS = \[\]/ALLOWED_HOSTS = ['*']/" netbox/netbox/configuration.py && \
    sed -i "s/SECRET_KEY = ''/SECRET_KEY = 'netboxci'/" netbox/netbox/configuration.py && \
    sed -i "s/'USER': '',/'USER': 'netbox',/" netbox/netbox/configuration.py && \
    sed -i "s/CACHE_TIMEOUT = 900/CACHE_TIMEOUT = 0/" netbox/netbox/configuration.py

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

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
