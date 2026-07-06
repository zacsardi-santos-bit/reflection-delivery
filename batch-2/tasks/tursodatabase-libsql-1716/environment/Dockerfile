FROM rust:1.80

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libclang-dev protobuf-compiler \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/tursodatabase/libsql.git && \
    git fetch --depth 30 origin 20147731c4a9d6e942b159f183215e9399fda2c3 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN curl -LsSf https://get.nexte.st/0.9.85/linux | tar zxf - -C /usr/local/cargo/bin

RUN cargo fetch

WORKDIR /workspace



# --- offline-cargo-bake (idempotent) ---
RUN printf %s 'ZGlmZiAtLWdpdCBhL0NhcmdvLmxvY2sgYi9DYXJnby5sb2NrCmluZGV4IGNjNWJhNDI4ZmEuLmJmMDlmYTJlZGQgMTAwNjQ0Ci0tLSBhL0NhcmdvLmxvY2sKKysrIGIvQ2FyZ28ubG9jawpAQCAtMTM0Nyw2ICsxMzQ3LDcgQEAgZGVwZW5kZW5jaWVzID0gWwogICJlbmNvZGVfdW5pY29kZSIsCiAgImxhenlfc3RhdGljIiwKICAibGliYyIsCisgInVuaWNvZGUtd2lkdGgiLAogICJ3aW5kb3dzLXN5cyAwLjUyLjAiLAogXQogCkBAIC0xODIyLDYgKzE4MjMsMTkgQEAgZGVwZW5kZW5jaWVzID0gWwogICJzeW4gMi4wLjcwIiwKIF0KIAorW1twYWNrYWdlXV0KK25hbWUgPSAiZGlhbG9ndWVyIgordmVyc2lvbiA9ICIwLjExLjAiCitzb3VyY2UgPSAicmVnaXN0cnkraHR0cHM6Ly9naXRodWIuY29tL3J1c3QtbGFuZy9jcmF0ZXMuaW8taW5kZXgiCitjaGVja3N1bSA9ICI2NThiY2U4MDVkNzcwZjQwN2JjNjIxMDJmY2E3YzJjNjRjZWVmMmZiY2IyYjhiZDE5ZDI3NjVjZTA5Mzk4MGRlIgorZGVwZW5kZW5jaWVzID0gWworICJjb25zb2xlIiwKKyAic2hlbGwtd29yZHMiLAorICJ0ZW1wZmlsZSIsCisgInRoaXNlcnJvciIsCisgInplcm9pemUiLAorXQorCiBbW3BhY2thZ2VdXQogbmFtZSA9ICJkaWdlc3QiCiB2ZXJzaW9uID0gIjAuMTAuNyIKQEAgLTMxNjcsNiArMzE4MSw3IEBAIGRlcGVuZGVuY2llcyA9IFsKICAiY29uc29sZS1zdWJzY3JpYmVyIiwKICAiY3JjIiwKICAiY3Jvc3NiZWFtIiwKKyAiZGlhbG9ndWVyIiwKICAiZW5jbG9zZSIsCiAgImVudl9sb2dnZXIiLAogICJmYWxsaWJsZS1pdGVyYXRvciAwLjMuMCIsCkBAIC0zMjA1LDYgKzMyMjAsNyBAQCBkZXBlbmRlbmNpZXMgPSBbCiAgInByaW9yaXR5LXF1ZXVlIDEuNC4wIiwKICAicHJvcHRlc3QiLAogICJwcm9zdCIsCisgInByb3N0LWJ1aWxkIiwKICAicmFuZCIsCiAgInJlZ2V4IiwKICAicmVxd2VzdCIsCkBAIC0zMjI3LDYgKzMyNDMsNyBAQCBkZXBlbmRlbmNpZXMgPSBbCiAgInRva2lvLXR1bmdzdGVuaXRlIiwKICAidG9raW8tdXRpbCIsCiAgInRvbmljIDAuMTEuMCIsCisgInRvbmljLWJ1aWxkIDAuMTEuMCIsCiAgInRvbmljLXdlYiIsCiAgInRvd2VyIiwKICAidG93ZXItaHR0cCAwLjMuNSIsCkBAIC01MjE2LDYgKzUyMzMsMTIgQEAgZGVwZW5kZW5jaWVzID0gWwogICJsYXp5X3N0YXRpYyIsCiBdCiAKK1tbcGFja2FnZV1dCituYW1lID0gInNoZWxsLXdvcmRzIgordmVyc2lvbiA9ICIxLjEuMCIKK3NvdXJjZSA9ICJyZWdpc3RyeStodHRwczovL2dpdGh1Yi5jb20vcnVzdC1sYW5nL2NyYXRlcy5pby1pbmRleCIKK2NoZWNrc3VtID0gIjI0MTg4YTY3NmI2YWU2OGMzYjJjYjNhMDFiZTE3ZmJmNzI0MGNlMDA5Nzk5YmI1NmQ1YjE0MDkwNTFlNzhmZGUiCisKIFtbcGFja2FnZV1dCiBuYW1lID0gInNoZWxsZXhwYW5kIgogdmVyc2lvbiA9ICIyLjEuMiIKZGlmZiAtLWdpdCBhL2xpYnNxbC1zZXJ2ZXIvQ2FyZ28udG9tbCBiL2xpYnNxbC1zZXJ2ZXIvQ2FyZ28udG9tbAppbmRleCA0YmNhYTJhOTMzLi5lZGViNTk4OGQ0IDEwMDY0NAotLS0gYS9saWJzcWwtc2VydmVyL0NhcmdvLnRvbWwKKysrIGIvbGlic3FsLXNlcnZlci9DYXJnby50b21sCkBAIC05NSw2ICs5NSw3IEBAIHRhciA9ICIwLjQuNDEiCiBhd3MtY29uZmlnID0gIjEiCiBhd3Mtc2RrLXMzID0gIjEiCiBhd3Mtc21pdGh5LXJ1bnRpbWUgPSAiMS42LjIiCitkaWFsb2d1ZXIgPSB7IHZlcnNpb24gPSAiMC4xMS4wIiwgZmVhdHVyZXMgPSBbImhpc3RvcnkiXSB9CiAKIFtkZXYtZGVwZW5kZW5jaWVzXQogYXJiaXRyYXJ5ID0geyB2ZXJzaW9uID0gIjEuMy4wIiwgZmVhdHVyZXMgPSBbImRlcml2ZV9hcmJpdHJhcnkiXSB9CkBAIC0xMTIsNiArMTEzLDggQEAgbWV0cmljcy11dGlsID0gIjAuMTUiCiBzM3MgPSAiMC44LjEiCiBzM3MtZnMgPSAiMC44LjEiCiByaW5nID0geyB2ZXJzaW9uID0gIjAuMTcuOCIsIGZlYXR1cmVzID0gWyJzdGQiXSB9Cit0b25pYy1idWlsZCA9ICIwLjExIgorcHJvc3QtYnVpbGQgPSAiMC4xMiIKIAogW2J1aWxkLWRlcGVuZGVuY2llc10KIHZlcmdlbiA9IHsgdmVyc2lvbiA9ICI4IiwgZmVhdHVyZXMgPSBbImJ1aWxkIiwgImdpdCIsICJnaXRjbCJdIH0K' | base64 -d > /tmp/_golden_toml.patch
RUN printf %s 'ZGlmZiAtLWdpdCBhL2xpYnNxbC1zZXJ2ZXIvdGVzdHMvYm9vdHN0cmFwLnJzIGIvbGlic3FsLXNlcnZlci90ZXN0cy9ib290c3RyYXAucnMKbmV3IGZpbGUgbW9kZSAxMDA2NDQKaW5kZXggMDAwMDAwMDAwMC4uYTQ2NGY1MzI4OAotLS0gL2Rldi9udWxsCisrKyBiL2xpYnNxbC1zZXJ2ZXIvdGVzdHMvYm9vdHN0cmFwLnJzCkBAIC0wLDAgKzEsMzIgQEAKK3VzZSBzdGQ6OnBhdGg6OlBhdGhCdWY7Cit1c2Ugc3RkOjpwcm9jZXNzOjpDb21tYW5kOworCisjW3Rlc3RdCitmbiBib290c3RyYXAoKSB7CisgICAgbGV0IGlmYWNlX2ZpbGVzID0gJlsicHJvdG8vYWRtaW5fc2hlbGwucHJvdG8iXTsKKyAgICBsZXQgZGlycyA9ICZbInByb3RvIl07CisKKyAgICBsZXQgb3V0X2RpciA9IFBhdGhCdWY6OmZyb20oc3RkOjplbnYhKCJDQVJHT19NQU5JRkVTVF9ESVIiKSkKKyAgICAgICAgLmpvaW4oInNyYyIpCisgICAgICAgIC5qb2luKCJnZW5lcmF0ZWQiKTsKKworICAgIGxldCBjb25maWcgPSBwcm9zdF9idWlsZDo6Q29uZmlnOjpuZXcoKTsKKworICAgIHRvbmljX2J1aWxkOjpjb25maWd1cmUoKQorICAgICAgICAuYnVpbGRfY2xpZW50KHRydWUpCisgICAgICAgIC5idWlsZF9zZXJ2ZXIodHJ1ZSkKKyAgICAgICAgLmJ1aWxkX3RyYW5zcG9ydCh0cnVlKQorICAgICAgICAub3V0X2Rpcigmb3V0X2RpcikKKyAgICAgICAgLmNvbXBpbGVfd2l0aF9jb25maWcoY29uZmlnLCBpZmFjZV9maWxlcywgZGlycykKKyAgICAgICAgLnVud3JhcCgpOworCisgICAgbGV0IHN0YXR1cyA9IENvbW1hbmQ6Om5ldygiZ2l0IikKKyAgICAgICAgLmFyZygiZGlmZiIpCisgICAgICAgIC5hcmcoIi0tZXhpdC1jb2RlIikKKyAgICAgICAgLmFyZygiLS0iKQorICAgICAgICAuYXJnKCZvdXRfZGlyKQorICAgICAgICAuc3RhdHVzKCkKKyAgICAgICAgLnVud3JhcCgpOworCisgICAgYXNzZXJ0IShzdGF0dXMuc3VjY2VzcygpLCAiWW91IHNob3VsZCBjb21taXQgdGhlIHByb3RvYnVmIGZpbGVzIik7Cit9Cg==' | base64 -d > /tmp/_test.patch
RUN cd /workspace/repo && git ls-files --others --exclude-standard -z | tar --null -T - -czf /tmp/_pre_untracked.tgz 2>/dev/null || true
RUN (cd /workspace/repo && (git apply --ignore-whitespace --whitespace=nowarn /tmp/_golden_toml.patch 2>/dev/null || patch -p1 --fuzz=5 -i /tmp/_golden_toml.patch 2>/dev/null || true)); (cd /workspace/repo && (git apply --ignore-whitespace --whitespace=nowarn /tmp/_test.patch 2>/dev/null || patch -p1 --fuzz=5 -i /tmp/_test.patch 2>/dev/null || true)); true \
    && ( cd /workspace/repo && (cargo fetch --locked 2>&1 | tail -2 || cargo fetch 2>&1 | tail -2 || true); (cargo metadata --format-version=1 >/dev/null 2>&1 || true); (cargo test --no-run -p libsql-server 2>&1 | tail -2 || cargo test --no-run 2>&1 | tail -2 || true); (cargo nextest run --no-run -p libsql-server 2>&1 | tail -2 || cargo nextest run --no-run 2>&1 | tail -2 || true) ) ; \
    cd /workspace/repo && git checkout -- . 2>/dev/null; git clean -fdq 2>/dev/null; tar -xzf /tmp/_pre_untracked.tgz -C /workspace/repo 2>/dev/null; rm -f /tmp/_pre_untracked.tgz /tmp/_golden_toml.patch /tmp/_test.patch 2>/dev/null || true
ENV CARGO_NET_OFFLINE=true

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

# OFFLINE-CARGO-BAKE
RUN cd /workspace/repo && (cargo fetch 2>/dev/null; cargo test --no-run 2>/dev/null; cargo nextest run --no-run 2>/dev/null; true)
ENV CARGO_NET_OFFLINE=true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
