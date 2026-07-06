FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ray-project/ray.git && \
    git fetch --depth 30 origin 443b01de5c83995bb3acb9191c9421e66eabe1da && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip pydantic

RUN echo "aW1wb3J0IG9zCmltcG9ydCBzaXRlCmZyb20gcGF0aGxpYiBpbXBvcnQgUGF0aAoKYmFzZSA9IFBhdGgoc2l0ZS5nZXRzaXRlcGFja2FnZXMoKVswXSkgLyAndmxsbScKCmRpcnMgPSBbCiAgICAndjEvZW5naW5lJywKICAgICdlbnRyeXBvaW50cy9vcGVuYWkvY2hhdF9jb21wbGV0aW9uJywKICAgICdlbnRyeXBvaW50cy9vcGVuYWkvY29tcGxldGlvbicsCiAgICAnZW50cnlwb2ludHMvb3BlbmFpL2VuZ2luZScsCiAgICAnZW50cnlwb2ludHMvb3BlbmFpL3NwZWVjaF90b190ZXh0JywKICAgICdlbnRyeXBvaW50cy9wb29saW5nL2VtYmVkJywKICAgICdlbnRyeXBvaW50cy9wb29saW5nL3Njb3JlJywKICAgICdlbnRyeXBvaW50cy9zZXJ2ZS90b2tlbml6ZScsCl0KCmZvciBkIGluIGRpcnM6CiAgICBwID0gYmFzZSAvIGQKICAgIHAubWtkaXIocGFyZW50cz1UcnVlLCBleGlzdF9vaz1UcnVlKQogICAgcGFydHMgPSBkLnNwbGl0KCcvJykKICAgIGZvciBpIGluIHJhbmdlKGxlbihwYXJ0cykgKyAxKToKICAgICAgICBkcCA9IGJhc2UgLyBQYXRoKCpwYXJ0c1s6aV0pIGlmIGkgPiAwIGVsc2UgYmFzZQogICAgICAgIGlwID0gZHAgLyAnX19pbml0X18ucHknCiAgICAgICAgaWYgbm90IGlwLmV4aXN0cygpOgogICAgICAgICAgICBpcC50b3VjaCgpCgpzdHViID0gJycnZnJvbSBweWRhbnRpYyBpbXBvcnQgQmFzZU1vZGVsCmZyb20gdHlwaW5nIGltcG9ydCBPcHRpb25hbCwgQW55CgpjbGFzcyBfQihCYXNlTW9kZWwpOiBwYXNzCgpjbGFzcyBDaGF0Q29tcGxldGlvblJlcXVlc3QoX0IpOiBwYXNzCmNsYXNzIENoYXRDb21wbGV0aW9uUmVzcG9uc2UoX0IpOiBwYXNzCmNsYXNzIENoYXRDb21wbGV0aW9uU3RyZWFtUmVzcG9uc2UoX0IpOiBwYXNzCmNsYXNzIENvbXBsZXRpb25SZXF1ZXN0KF9CKTogcGFzcwpjbGFzcyBDb21wbGV0aW9uUmVzcG9uc2UoX0IpOiBwYXNzCmNsYXNzIENvbXBsZXRpb25TdHJlYW1SZXNwb25zZShfQik6IHBhc3MKY2xhc3MgRXJyb3JJbmZvKF9CKToKICAgIG1lc3NhZ2U6IHN0ciA9ICIiCiAgICB0eXBlOiBzdHIgPSAiIgogICAgcGFyYW06IE9wdGlvbmFsW3N0cl0gPSBOb25lCiAgICBjb2RlOiBpbnQgPSA1MDAKY2xhc3MgRXJyb3JSZXNwb25zZShfQik6CiAgICBlcnJvcjogT3B0aW9uYWxbQW55XSA9IE5vbmUKY2xhc3MgVHJhbnNjcmlwdGlvblJlcXVlc3QoX0IpOiBwYXNzCmNsYXNzIFRyYW5zY3JpcHRpb25SZXNwb25zZShfQik6IHBhc3MKY2xhc3MgVHJhbnNjcmlwdGlvblN0cmVhbVJlc3BvbnNlKF9CKTogcGFzcwpjbGFzcyBFbWJlZGRpbmdDaGF0UmVxdWVzdChfQik6IHBhc3MKY2xhc3MgRW1iZWRkaW5nQ29tcGxldGlvblJlcXVlc3QoX0IpOiBwYXNzCmNsYXNzIEVtYmVkZGluZ1Jlc3BvbnNlKF9CKTogcGFzcwpjbGFzcyBTY29yZVJlc3BvbnNlKF9CKTogcGFzcwpjbGFzcyBTY29yZVRleHRSZXF1ZXN0KF9CKTogcGFzcwpjbGFzcyBEZXRva2VuaXplUmVxdWVzdChfQik6IHBhc3MKY2xhc3MgRGV0b2tlbml6ZVJlc3BvbnNlKF9CKTogcGFzcwpjbGFzcyBUb2tlbml6ZUNoYXRSZXF1ZXN0KF9CKTogcGFzcwpjbGFzcyBUb2tlbml6ZUNvbXBsZXRpb25SZXF1ZXN0KF9CKTogcGFzcwpjbGFzcyBUb2tlbml6ZVJlc3BvbnNlKF9CKTogcGFzcwonJycKCihiYXNlIC8gJ3YxJyAvICdlbmdpbmUnIC8gJ2V4Y2VwdGlvbnMucHknKS53cml0ZV90ZXh0KAogICAgImNsYXNzIEVuZ2luZURlYWRFcnJvcihFeGNlcHRpb24pOlxuICAgIHBhc3NcbiIKKQoKZm9yIGQgaW4gZGlyczoKICAgIChiYXNlIC8gZCAvICdwcm90b2NvbC5weScpLndyaXRlX3RleHQoc3R1YikKCnByaW50KCJ2bGxtIHN0dWIgY3JlYXRlZCBhdCIsIGJhc2UpCg==" | base64 -d > /tmp/create_vllm_stub.py && python3 /tmp/create_vllm_stub.py

RUN pip install --pre "ray[serve]" 2>&1 | tail -10

RUN pip install httpx fastapi starlette aiohttp grpcio 2>&1 | tail -5

RUN pip install pytest pytest-asyncio pytest-timeout 2>&1 | tail -5


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

# offline-harbor: git blob-drop shrink (client-contract .git<100MB, HEAD==base)
RUN set +e; cd /workspace/repo 2>/dev/null && ( git reflog expire --expire=now --all 2>/dev/null; rm -rf .git/logs; git remote remove origin 2>/dev/null; git tag -l | xargs -r git tag -d >/dev/null 2>&1; git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d 2>/dev/null; git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_keep.txt; if [ -s /tmp/_keep.txt ]; then git pack-objects --quiet /tmp/_kp < /tmp/_keep.txt >/dev/null 2>&1 && rm -rf .git/objects/pack && mkdir -p .git/objects/pack && find .git/objects -maxdepth 1 -type d -name '[0-9a-f][0-9a-f]' -exec rm -rf {} + 2>/dev/null; mv /tmp/_kp-*.pack /tmp/_kp-*.idx .git/objects/pack/ 2>/dev/null; fi; git rev-parse HEAD >/dev/null 2>&1 || echo "WARN head-broken"; du -sm .git 2>/dev/null | cut -f1 ) ; true
