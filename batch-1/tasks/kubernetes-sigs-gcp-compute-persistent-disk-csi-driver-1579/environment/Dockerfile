FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/kubernetes-sigs/gcp-compute-persistent-disk-csi-driver.git && \
    git fetch --depth 30 origin 616c9a69d869e40cc6039aabec2c280106642457 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

# Inject and commit a lint test that verifies klog printf-like functions do not
# use the %w error-wrapping verb (only valid in fmt.Errorf).  Committing the
# file ensures it survives the "git clean -fd" that the validator runs between
# the pre-patch and post-patch test runs.
RUN mkdir -p /workspace/repo/test/vet && \
    echo "cGFja2FnZSB2ZXRfdGVzdAoKaW1wb3J0ICgKCSJvcyIKCSJvcy9leGVjIgoJInBhdGgvZmlsZXBhdGgiCgkic3RyaW5ncyIKCSJ0ZXN0aW5nIgopCgovLyBmaW5kUmVwb1Jvb3Qgd2Fsa3MgdXAgdGhlIGRpcmVjdG9yeSB0cmVlIGZyb20gdGhlIHRlc3QncyB3b3JraW5nIGRpcmVjdG9yeQovLyB0byBmaW5kIHRoZSByZXBvc2l0b3J5IHJvb3QgKGlkZW50aWZpZWQgYnkgZ28ubW9kKS4KZnVuYyBmaW5kUmVwb1Jvb3QodCAqdGVzdGluZy5UKSBzdHJpbmcgewoJdC5IZWxwZXIoKQoJZGlyLCBfIDo9IG9zLkdldHdkKCkKCWZvciB7CgkJaWYgXywgZXJyIDo9IG9zLlN0YXQoZmlsZXBhdGguSm9pbihkaXIsICJnby5tb2QiKSk7IGVyciA9PSBuaWwgewoJCQlyZXR1cm4gZGlyCgkJfQoJCXBhcmVudCA6PSBmaWxlcGF0aC5EaXIoZGlyKQoJCWlmIHBhcmVudCA9PSBkaXIgewoJCQlicmVhawoJCX0KCQlkaXIgPSBwYXJlbnQKCX0KCXQuRmF0YWwoImNvdWxkIG5vdCBmaW5kIHJlcG9zaXRvcnkgcm9vdCAobm8gZ28ubW9kIGZvdW5kKSIpCglyZXR1cm4gIiIgLy8gdW5yZWFjaGFibGU7IHNhdGlzZmllcyBjb21waWxlcgp9CgovLyBUZXN0S2xvZ05vV1ZlcmIgdmVyaWZpZXMgdGhhdCBrbG9nIHByaW50Zi1saWtlIGZ1bmN0aW9ucyBkbyBub3QgdXNlIHRoZSAldwovLyBlcnJvci13cmFwcGluZyB2ZXJiLiBUaGUgJXcgdmVyYiBpcyBvbmx5IHZhbGlkIGluIGZtdC5FcnJvcmYgZm9yIHdyYXBwaW5nCi8vIGVycm9yczsgdXNpbmcgaXQgaW4ga2xvZy5FcnJvcmYvV2FybmluZ2YvSW5mb2YvRmF0YWxmIHByb2R1Y2VzIGdhcmJsZWQgbG9nCi8vIG91dHB1dCAoZS5nLiAiJSF3KCplcnJvcnMuZXJyb3JTdHJpbmc9Jnttc2d9KSIpIGluc3RlYWQgb2YgdGhlIGVycm9yIHN0cmluZy4KZnVuYyBUZXN0S2xvZ05vV1ZlcmIodCAqdGVzdGluZy5UKSB7Cglyb290IDo9IGZpbmRSZXBvUm9vdCh0KQoKCXZhciBzZWFyY2hEaXJzIFtdc3RyaW5nCglmb3IgXywgZCA6PSByYW5nZSBbXXN0cmluZ3sicGtnIiwgImNtZCIsICJ0ZXN0In0gewoJCWZ1bGwgOj0gZmlsZXBhdGguSm9pbihyb290LCBkKQoJCWlmIF8sIGVyciA6PSBvcy5TdGF0KGZ1bGwpOyBlcnIgPT0gbmlsIHsKCQkJc2VhcmNoRGlycyA9IGFwcGVuZChzZWFyY2hEaXJzLCBmdWxsKQoJCX0KCX0KCWlmIGxlbihzZWFyY2hEaXJzKSA9PSAwIHsKCQl0LkZhdGFsKCJubyBzb3VyY2UgZGlyZWN0b3JpZXMgZm91bmQgdG8gc2VhcmNoIikKCX0KCgkvLyBVc2UgZ3JlcCB0byBmaW5kIGtsb2cgcHJpbnRmLXN0eWxlIGNhbGxzIHRoYXQgY29udGFpbiB0aGUgJXcgdmVyYi4KCS8vIGdyZXAgZXhpdHMgMCB3aGVuIG1hdGNoZXMgYXJlIGZvdW5kLCAxIHdoZW4gbm9uZSBhcmUgZm91bmQsIDIgb24gZXJyb3IuCgkvLyBXZSBjYXB0dXJlIHN0ZG91dCBvbmx5OyBhIG5vbi16ZXJvIGV4aXQgKG5vIG1hdGNoZXMpIGlzIHRoZSBkZXNpcmVkIHN0YXRlLgoJYXJncyA6PSBhcHBlbmQoCgkJW11zdHJpbmd7Ii1ybiIsICItLWluY2x1ZGU9Ki5nbyIsICItRSIsICJrbG9nXFwuKEVycm9yZnxXYXJuaW5nZnxJbmZvZnxGYXRhbGYpLioldyJ9LAoJCXNlYXJjaERpcnMuLi4sCgkpCglvdXQsIF8gOj0gZXhlYy5Db21tYW5kKCJncmVwIiwgYXJncy4uLikuT3V0cHV0KCkKCglpZiBtYXRjaGVzIDo9IHN0cmluZ3MuVHJpbVNwYWNlKHN0cmluZyhvdXQpKTsgbWF0Y2hlcyAhPSAiIiB7CgkJdC5FcnJvcmYoImtsb2cgZm9ybWF0IGZ1bmN0aW9ucyBkbyBub3Qgc3VwcG9ydCAlJXcgKHVzZSAlJXYgaW5zdGVhZCk6XG4lcyIsIG1hdGNoZXMpCgl9Cn0K" | base64 -d > /workspace/repo/test/vet/klog_format_test.go && \
    cd /workspace/repo && \
    git config user.email "ci@test.local" && \
    git config user.name "CI Test" && \
    git add test/vet/klog_format_test.go && \
    git commit -m "test: add klog format verb lint test" && git reset --soft 616c9a69d869e40cc6039aabec2c280106642457 && rm -rf .git/logs .git/refs/original 2>/dev/null

WORKDIR /workspace/repo

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
