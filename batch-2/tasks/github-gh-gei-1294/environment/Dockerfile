FROM mcr.microsoft.com/dotnet/sdk:6.0

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch python3 \
    && rm -rf /var/lib/apt/lists/*

RUN printf 'import xml.etree.ElementTree as ET\ntry:\n    root = ET.parse("/tmp/junit-results.xml").getroot()\n    for tc in root.iter("testcase"):\n        classname = tc.get("classname", "")\n        name = tc.get("name", "")\n        full = classname + "::" + name\n        fail = tc.find("failure") is not None or tc.find("error") is not None\n        status = "FAILED" if fail else "PASSED"\n        print(full + " " + status)\nexcept:\n    pass\n' > /tmp/parse_junit.py

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/github/gh-gei.git && \
    git fetch --depth 30 origin 236b3fe19fbb7996b4db6ad7ad1e0da90374d14d && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN dotnet restore src/OctoshiftCLI.sln

RUN dotnet build src/OctoshiftCLI.sln --no-restore /p:TreatWarningsAsErrors=false

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
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
