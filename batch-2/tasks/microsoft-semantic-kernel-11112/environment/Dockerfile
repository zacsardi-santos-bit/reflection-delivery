FROM mcr.microsoft.com/dotnet/sdk:8.0

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN printf '%s\n' 'import sys,re' 'for line in sys.stdin:' '    print(line.rstrip())' '    m=re.match(r"^\s+(Passed|Failed) (.*)",line)' '    if m:' '        parts=m.group(2).split(".")' '        if len(parts)>=2:' '            cls=parts[-2]' '            method=re.sub(r"[\s(].*","",parts[-1])' '            status="PASSED"if m.group(1)=="Passed"else"FAILED"' '            print(cls+"::"+method+" "+status)' > /usr/local/bin/parse_dotnet.py

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/microsoft/semantic-kernel.git && \
    git fetch --depth 30 origin 89de29ceb6eb7c2e8bb2eed2c4915b659824e741 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cd /workspace/repo/dotnet && dotnet restore src/Connectors/Connectors.Postgres.UnitTests/Connectors.Postgres.UnitTests.csproj

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
