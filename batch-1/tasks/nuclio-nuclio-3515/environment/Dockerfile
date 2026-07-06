FROM golang:1.23

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

RUN echo "IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwppbXBvcnQgc3lzLCBzdWJwcm9jZXNzLCBvcwoKYXJncyA9IHN5cy5hcmd2WzE6XQoKaWYgYXJncyBhbmQgYXJnc1swXSA9PSAnYXBwbHknIGFuZCBhbnkoJ2dvbGRlbi5wYXRjaCcgaW4gYSBmb3IgYSBpbiBhcmdzKToKICAgIHBhdGNoX2ZpbGUgPSBuZXh0KChhIGZvciBhIGluIGFyZ3MgaWYgJ2dvbGRlbi5wYXRjaCcgaW4gYSksIE5vbmUpCiAgICBpZiBwYXRjaF9maWxlIGFuZCBvcy5wYXRoLmV4aXN0cyhwYXRjaF9maWxlKToKICAgICAgICB3aXRoIG9wZW4ocGF0Y2hfZmlsZSkgYXMgZjoKICAgICAgICAgICAgbGluZXMgPSBmLnJlYWRsaW5lcygpCiAgICAgICAgZmlsdGVyZWQsIHNraXAgPSBbXSwgRmFsc2UKICAgICAgICBmb3IgbGluZSBpbiBsaW5lczoKICAgICAgICAgICAgaWYgbGluZS5zdGFydHN3aXRoKCdkaWZmIC0tZ2l0ICcpIGFuZCAnLnBuZycgaW4gbGluZToKICAgICAgICAgICAgICAgIHNraXAgPSBUcnVlCiAgICAgICAgICAgIGVsaWYgbGluZS5zdGFydHN3aXRoKCdkaWZmIC0tZ2l0ICcpIGFuZCBza2lwOgogICAgICAgICAgICAgICAgc2tpcCA9IEZhbHNlCiAgICAgICAgICAgIGlmIG5vdCBza2lwOgogICAgICAgICAgICAgICAgZmlsdGVyZWQuYXBwZW5kKGxpbmUpCiAgICAgICAgcGF0Y2ggPSAnJy5qb2luKGZpbHRlcmVkKQogICAgICAgIHJlc3VsdCA9IHN1YnByb2Nlc3MucnVuKFsnL3Vzci9iaW4vZ2l0JywgJ2FwcGx5JywgJy0nXSwgaW5wdXQ9cGF0Y2gsIHRleHQ9VHJ1ZSkKICAgICAgICBzeXMuZXhpdChyZXN1bHQucmV0dXJuY29kZSkKICAgIGVsc2U6CiAgICAgICAgb3MuZXhlY3YoJy91c3IvYmluL2dpdCcsIFsnL3Vzci9iaW4vZ2l0J10gKyBhcmdzKQplbHNlOgogICAgb3MuZXhlY3YoJy91c3IvYmluL2dpdCcsIFsnL3Vzci9iaW4vZ2l0J10gKyBhcmdzKQo=" | base64 -d > /usr/local/bin/git && chmod +x /usr/local/bin/git

WORKDIR /workspace

RUN /usr/bin/git init /workspace/repo && cd /workspace/repo && \
    /usr/bin/git remote add origin https://github.com/nuclio/nuclio.git && \
    /usr/bin/git fetch --depth 30 origin 592f1d0dec612c23e04c6e047bd2bed35111a7d9 && \
    /usr/bin/git checkout --detach FETCH_HEAD && \
    /usr/bin/git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    /usr/bin/git remote remove origin && \
    /usr/bin/git tag -l | xargs -r /usr/bin/git tag -d && \
    /usr/bin/git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 /usr/bin/git update-ref -d && \
    /usr/bin/git reflog expire --expire=now --all && \
    /usr/bin/git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download || true

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
