FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin db7eab370eb07e8a4d0ef4d864dadbad7a86c66f && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install -e airflow-ctl/

RUN pip install --no-deps -e devel-common/

RUN pip install pytest pytest-mock "time-machine[dateutil]" keyring "keyrings.alt" pytest-asyncio

RUN python3 -c "import base64; exec(base64.b64decode('aW1wb3J0IGhhc2hsaWIsIGpzb24KZnJvbSBwYXRobGliIGltcG9ydCBQYXRoCkRRID0gY2hyKDM0KQpyb290ID0gUGF0aCgiL3dvcmtzcGFjZS9yZXBvIikKY29udGVudCA9IChyb290IC8gInB5cHJvamVjdC50b21sIikucmVhZF90ZXh0KCkuc3BsaXRsaW5lcygpCmluX3dzID0gRmFsc2UKZmlsZXMgPSBbXQpmb3IgbGluZSBpbiBjb250ZW50OgogICAgdCA9IGxpbmUuc3RyaXAoKQogICAgaWYgbm90IGluX3dzIGFuZCB0LnN0YXJ0c3dpdGgoIlt0b29sLnV2LndvcmtzcGFjZV0iKToKICAgICAgICBpbl93cyA9IFRydWUKICAgIGVsaWYgaW5fd3M6CiAgICAgICAgaWYgdC5zdGFydHN3aXRoKCIjIik6CiAgICAgICAgICAgIGNvbnRpbnVlCiAgICAgICAgaWYgdC5zdGFydHN3aXRoKERRKToKICAgICAgICAgICAgcGF0aCA9IHQuc3BsaXQoRFEpWzFdCiAgICAgICAgICAgIGlmIHQuc3RhcnRzd2l0aChEUSArICJwcm92aWRlcnMvIik6CiAgICAgICAgICAgICAgICBmaWxlcy5leHRlbmQoW3Jvb3QgLyBwYXRoIC8gInB5cHJvamVjdC50b21sIiwgcm9vdCAvIHBhdGggLyAicHJvdmlkZXIueWFtbCJdKQogICAgICAgIGVsaWYgdC5zdGFydHN3aXRoKCJdIik6CiAgICAgICAgICAgIGJyZWFrCmhhc2hlciA9IGhhc2hsaWIuc2hhMjU2KCkKZm9yIGYgaW4gc29ydGVkKGZpbGVzKToKICAgIGhhc2hlci51cGRhdGUoZi5yZWFkX2J5dGVzKCkpCmggPSBoYXNoZXIuaGV4ZGlnZXN0KCkKZ2VuID0gcm9vdCAvICJnZW5lcmF0ZWQiCihnZW4gLyAicHJvdmlkZXJfZGVwZW5kZW5jaWVzLmpzb24iKS53cml0ZV90ZXh0KGpzb24uZHVtcHMoe30pKQooZ2VuIC8gInByb3ZpZGVyX2RlcGVuZGVuY2llcy5qc29uLnNoYTI1NnN1bSIpLndyaXRlX3RleHQoaCkKcHJpbnQoIkhhc2g6IiwgaCkK').decode())"


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
# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` (the client
# verifier exec) keeps go/cargo/java/etc on PATH after /etc/profile resets it.
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
