FROM node:20

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/typeorm/typeorm.git && \
    git fetch --depth 30 origin a909d5b6975f0feb0c8759dd146dba8f1e3ad168 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN npm ci 2>&1 | tail -20

RUN echo '[{"skip":false,"name":"sqlite","type":"sqlite","database":"./temp/sqlitedb-1.db","logging":false,"relationLoadStrategy":"join"},{"skip":false,"name":"sqlite-2","type":"sqlite","database":"./temp/sqlitedb-2.db","logging":false,"relationLoadStrategy":"query"},{"skip":false,"name":"better-sqlite3","type":"better-sqlite3","database":"temp/better-sqlite3db.db","logging":false},{"skip":true,"name":"mysql","type":"mysql","host":"localhost","port":3306,"username":"root","password":"admin","database":"test","logging":false},{"skip":true,"name":"mariadb","type":"mariadb","host":"localhost","port":3307,"username":"root","password":"admin","database":"test","logging":false},{"skip":true,"name":"postgres","type":"postgres","host":"localhost","port":5432,"username":"test","password":"test","database":"test","logging":false},{"skip":true,"name":"mssql","type":"mssql","host":"localhost","username":"sa","password":"Admin12345","database":"tempdb","logging":false},{"skip":true,"name":"cockroachdb","type":"cockroachdb","host":"localhost","port":26257,"username":"root","password":"","database":"defaultdb"},{"skip":true,"name":"mongodb","type":"mongodb","database":"test","logging":false}]' > /workspace/repo/ormconfig.json

RUN mkdir -p /workspace/repo/temp

WORKDIR /workspace

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

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
