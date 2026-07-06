FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/mlc-ai/mlc-llm.git && \
    git fetch --depth 1 origin 18e11f0b1a99600b38c2fb90f26c209ea4998632 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs .git/modules

RUN pip install pytest

RUN mkdir -p /opt/mocks/tvm /opt/mocks/mlc_chat/model /opt/mocks/mlc_chat/support

RUN echo 'Y2xhc3MgVGFyZ2V0OgogICAgZGVmIF9faW5pdF9fKHNlbGYsIHNwZWM9Tm9uZSk6CiAgICAgICAgaWYgaXNpbnN0YW5jZShzcGVjLCBkaWN0KToKICAgICAgICAgICAgc2VsZi5fa2luZCA9IHNwZWMuZ2V0KCdraW5kJywgJ2xsdm0nKQogICAgICAgIGVsc2U6CiAgICAgICAgICAgIHNlbGYuX2tpbmQgPSBzdHIoc3BlYykgaWYgc3BlYyBlbHNlICdsbHZtJwogICAgZGVmIF9fc3RyX18oc2VsZik6CiAgICAgICAgcmV0dXJuIHNlbGYuX2tpbmQKICAgIGRlZiBleHBvcnQoc2VsZik6CiAgICAgICAgcmV0dXJuIHsna2luZCc6IHNlbGYuX2tpbmR9CgpjbGFzcyBfVGFyZ2V0TW9kOgogICAgVGFyZ2V0ID0gVGFyZ2V0Cgp0YXJnZXQgPSBfVGFyZ2V0TW9kKCkK' | base64 -d > /opt/mocks/tvm/__init__.py

RUN echo 'TU9ERUxfUFJFU0VUUyA9IHsKICAgICJwaGktMV81IjogeyJtb2RlbF90eXBlIjogInBoaS1tc2Z0In0sCiAgICAicGhpLTIiOiB7Im1vZGVsX3R5cGUiOiAicGhpLW1zZnQifSwKfQo=' | base64 -d > /opt/mocks/mlc_chat/model/__init__.py

RUN echo 'TUxDX1RFTVBfRElSID0gTm9uZQo=' | base64 -d > /opt/mocks/mlc_chat/support/constants.py

RUN touch /opt/mocks/mlc_chat/__init__.py /opt/mocks/mlc_chat/support/__init__.py

RUN echo 'aW1wb3J0IHN5cwppbXBvcnQgb3MKCmRlZiBtYWluKCk6CiAgICBpZiBsZW4oc3lzLmFyZ3YpIDwgMyBvciBzeXMuYXJndlsxXSAhPSAiY29tcGlsZSI6CiAgICAgICAgc3lzLmV4aXQoMSkKICAgIGFyZ3MgPSBzeXMuYXJndlsyOl0KICAgIG1vZGVsID0gYXJnc1swXSBpZiBhcmdzIGFuZCBub3QgYXJnc1swXS5zdGFydHN3aXRoKCItIikgZWxzZSBOb25lCiAgICBxdWFudCA9IE5vbmUKICAgIGZvciBpLCBhcmcgaW4gZW51bWVyYXRlKGFyZ3MpOgogICAgICAgIGlmIGFyZyA9PSAiLS1xdWFudGl6YXRpb24iIGFuZCBpICsgMSA8IGxlbihhcmdzKToKICAgICAgICAgICAgcXVhbnQgPSBhcmdzW2kgKyAxXQogICAgdHJ5OgogICAgICAgIHdpdGggb3BlbigiL3dvcmtzcGFjZS9yZXBvL3B5dGhvbi9tbGNfY2hhdC9vcC9hdHRlbnRpb24ucHkiKSBhcyBmOgogICAgICAgICAgICBjb250ZW50ID0gZi5yZWFkKCkKICAgICAgICBpc19wb3N0X3BhdGNoID0gInFrX2R0eXBlIiBub3QgaW4gY29udGVudAogICAgZXhjZXB0IEV4Y2VwdGlvbjoKICAgICAgICBpc19wb3N0X3BhdGNoID0gVHJ1ZQogICAgcGhpX21vZGVscyA9IFsicGhpLTFfNSIsICJwaGktMiJdCiAgICBmMTZfcXVhbnRzID0gWyJxMGYxNiIsICJxM2YxNl8xIiwgInE0ZjE2XzEiXQogICAgaWYgbm90IGlzX3Bvc3RfcGF0Y2ggYW5kIG1vZGVsIGluIHBoaV9tb2RlbHMgYW5kIHF1YW50IGluIGYxNl9xdWFudHM6CiAgICAgICAgcHJpbnQoIkVycm9yOiB0eXBlIG1pc21hdGNoIGZvciB7fSB7fSIuZm9ybWF0KG1vZGVsLCBxdWFudCksIGZpbGU9c3lzLnN0ZGVycikKICAgICAgICBzeXMuZXhpdCgxKQogICAgcHJpbnQoIk9LOiB7fSB7fSIuZm9ybWF0KG1vZGVsLCBxdWFudCksIGZpbGU9c3lzLnN0ZGVycikKICAgIHN5cy5leGl0KDApCgptYWluKCkK' | base64 -d > /opt/mocks/mlc_chat/__main__.py

ENV PYTHONPATH=/opt/mocks

RUN mkdir -p /workspace/test-results

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
