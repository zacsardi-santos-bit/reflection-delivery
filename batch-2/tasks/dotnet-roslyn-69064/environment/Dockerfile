FROM mcr.microsoft.com/dotnet/sdk:8.0

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/dotnet/roslyn.git && \
    git fetch --depth 30 origin 9999d45981eb74581530c28d297f04ce9ba51004 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN printf '{"sdk":{"version":"8.0.100","rollForward":"latestFeature"},"msbuild-sdks":{"Microsoft.DotNet.Arcade.Sdk":"8.0.0-beta.23361.1","Microsoft.DotNet.Helix.Sdk":"8.0.0-beta.23361.1"}}' > global.json

RUN dotnet restore src/Compilers/CSharp/Test/Emit2/Microsoft.CodeAnalysis.CSharp.Emit2.UnitTests.csproj --framework net8.0 2>&1 | tail -30

RUN dotnet build src/Compilers/CSharp/Test/Emit2/Microsoft.CodeAnalysis.CSharp.Emit2.UnitTests.csproj --framework net8.0 -c Debug --no-restore 2>&1 | tail -30

# --- offline-nuget-warm (dotnet-cluster sentinel) ---
# Warm ~/.nuget cache AND leave obj/project.assets.json in place so the offline
# test-time `dotnet test` restore is a no-op served from the local cache.
# (No patches are applied here, so the tree must NOT be cleaned. The test command
# rewrites global.json itself at runtime to the same arcade/helix versions.)
ENV DOTNET_CLI_TELEMETRY_OPTOUT=1
ENV DOTNET_NOLOGO=1
ENV DOTNET_ROLL_FORWARD=LatestMajor
ENV NuGetAudit=false
# Restore WITHOUT --framework so the FULL multi-TFM project graph (net6.0/net7.0/netstandard2.0
# sibling projects + analyzers/ref packs) is cached -- the offline test-time `dotnet test`
# restores all referenced projects across all their frameworks, not just net8.0.
RUN cd /workspace/repo && \
    dotnet restore src/Compilers/CSharp/Test/Emit2/Microsoft.CodeAnalysis.CSharp.Emit2.UnitTests.csproj /p:NuGetAudit=false 2>&1 | tail -10 && \
    dotnet build src/Compilers/CSharp/Test/Emit2/Microsoft.CodeAnalysis.CSharp.Emit2.UnitTests.csproj --framework net8.0 -c Debug --no-restore /p:NuGetAudit=false 2>&1 | tail -10
# Pin offline NuGet config: overwrite every repo-root NuGet.config variant so the test
# sandbox restore uses only the warmed global-packages cache (zero network).
RUN cd /workspace/repo && \
    printf '<?xml version="1.0" encoding="utf-8"?>\n<configuration>\n  <packageSources>\n    <clear />\n  </packageSources>\n  <packageSourceMapping>\n    <clear />\n  </packageSourceMapping>\n  <auditSources>\n    <clear />\n  </auditSources>\n</configuration>\n' > /workspace/repo/NuGet.config && \
    for f in nuget.config NuGet.Config; do [ -e "/workspace/repo/$f" ] && cp /workspace/repo/NuGet.config "/workspace/repo/$f"; done; true
# --- end offline-nuget-warm ---

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

# OFFLINE-CARGO-BAKE
RUN cd /workspace/repo && (cargo fetch 2>/dev/null; cargo test --no-run 2>/dev/null; cargo nextest run --no-run 2>/dev/null; true)
ENV CARGO_NET_OFFLINE=true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
# --- offline toolchain fix: ensure the language toolchain (java) is installed on the login PATH ---
RUN command -v java >/dev/null 2>&1 \
    || (apt-get update && apt-get install -y --no-install-recommends default-jre-headless && rm -rf /var/lib/apt/lists/*) \
    || (apt-get update && apt-get install -y --no-install-recommends openjdk-17-jre-headless && rm -rf /var/lib/apt/lists/*) \
    || (apk add --no-cache openjdk17-jre-headless) || (dnf install -y java-17-openjdk-headless) || (yum install -y java-17-openjdk-headless) || true

RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
