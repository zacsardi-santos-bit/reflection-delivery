FROM python:3.6-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip setuptools wheel

RUN pip install numpy "cloudpickle==0.5.2" cython funcsigs click colorama psutil redis flatbuffers pyyaml six pytest

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ray-project/ray.git && \
    git fetch --depth 30 origin 22c7c87e1450a06bd0fdcfa7b6c1b15565b2f336 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN mkdir -p /workspace/stubs/ray

RUN python3 -c "import base64; open('/workspace/stubs/ray/__init__.py','w').write(base64.b64decode('ZnJvbSBfX2Z1dHVyZV9fIGltcG9ydCBhYnNvbHV0ZV9pbXBvcnQKaW1wb3J0IHRocmVhZGluZwoKX3N0b3JlID0ge30KX2N0ciA9IFswXQpfbG9jayA9IHRocmVhZGluZy5Mb2NrKCkKCgpkZWYgX3Vud3JhcCh2KToKICAgIGlmIGlzaW5zdGFuY2UodiwgX0Z1dHVyZSk6CiAgICAgICAgcmV0dXJuIHYuX3ZhbAogICAgcmV0dXJuIHYKCgpjbGFzcyBfRnV0dXJlKG9iamVjdCk6CiAgICBkZWYgX19pbml0X18oc2VsZiwgdmFsKToKICAgICAgICBzZWxmLl92YWwgPSB2YWwKCgpjbGFzcyBfTWV0aG9kKG9iamVjdCk6CiAgICBkZWYgX19pbml0X18oc2VsZiwgZnVuYyk6CiAgICAgICAgc2VsZi5fZnVuYyA9IGZ1bmMKCiAgICBkZWYgcmVtb3RlKHNlbGYsICphcmdzLCAqKmt3YXJncyk6CiAgICAgICAgYXJncyA9IHR1cGxlKF91bndyYXAoYSkgZm9yIGEgaW4gYXJncykKICAgICAgICBrd2FyZ3MgPSB7azogX3Vud3JhcCh2KSBmb3IgaywgdiBpbiBrd2FyZ3MuaXRlbXMoKX0KICAgICAgICByZXR1cm4gX0Z1dHVyZShzZWxmLl9mdW5jKCphcmdzLCAqKmt3YXJncykpCgoKY2xhc3MgX0FjdG9yKG9iamVjdCk6CiAgICBkZWYgX19pbml0X18oc2VsZiwgY2xzLCAqYXJncywgKiprd2FyZ3MpOgogICAgICAgIGFyZ3MgPSB0dXBsZShfdW53cmFwKGEpIGZvciBhIGluIGFyZ3MpCiAgICAgICAga3dhcmdzID0ge2s6IF91bndyYXAodikgZm9yIGssIHYgaW4ga3dhcmdzLml0ZW1zKCl9CiAgICAgICAgc2VsZi5fb2JqID0gY2xzKCphcmdzLCAqKmt3YXJncykKCiAgICBkZWYgX19nZXRhdHRyX18oc2VsZiwgbmFtZSk6CiAgICAgICAgaWYgbmFtZS5zdGFydHN3aXRoKCJfIik6CiAgICAgICAgICAgIHJhaXNlIEF0dHJpYnV0ZUVycm9yKG5hbWUpCiAgICAgICAgbWV0aCA9IGdldGF0dHIoc2VsZi5fb2JqLCBuYW1lKQogICAgICAgIHJldHVybiBfTWV0aG9kKG1ldGgpCgoKY2xhc3MgX1JlbW90ZUNsYXNzKG9iamVjdCk6CiAgICBkZWYgX19pbml0X18oc2VsZiwgY2xzKToKICAgICAgICBzZWxmLl9jbHMgPSBjbHMKCiAgICBkZWYgcmVtb3RlKHNlbGYsICphcmdzLCAqKmt3YXJncyk6CiAgICAgICAgcmV0dXJuIF9BY3RvcihzZWxmLl9jbHMsICphcmdzLCAqKmt3YXJncykKCgpjbGFzcyBfUmVtb3RlRnVuYyhvYmplY3QpOgogICAgZGVmIF9faW5pdF9fKHNlbGYsIGZuKToKICAgICAgICBzZWxmLl9mbiA9IGZuCgogICAgZGVmIHJlbW90ZShzZWxmLCAqYXJncywgKiprd2FyZ3MpOgogICAgICAgIGFyZ3MgPSB0dXBsZShfdW53cmFwKGEpIGZvciBhIGluIGFyZ3MpCiAgICAgICAga3dhcmdzID0ge2s6IF91bndyYXAodikgZm9yIGssIHYgaW4ga3dhcmdzLml0ZW1zKCl9CiAgICAgICAgcmV0dXJuIF9GdXR1cmUoc2VsZi5fZm4oKmFyZ3MsICoqa3dhcmdzKSkKCgpkZWYgcmVtb3RlKGY9Tm9uZSwgbnVtX2NwdXM9Tm9uZSwgbnVtX2dwdXM9Tm9uZSwgKiprdyk6CiAgICBpbXBvcnQgaW5zcGVjdAogICAgaWYgZiBpcyBOb25lOgogICAgICAgIGRlZiBkZWMoZyk6CiAgICAgICAgICAgIHJldHVybiBfUmVtb3RlQ2xhc3MoZykgaWYgaW5zcGVjdC5pc2NsYXNzKGcpIGVsc2UgX1JlbW90ZUZ1bmMoZykKICAgICAgICByZXR1cm4gZGVjCiAgICByZXR1cm4gX1JlbW90ZUNsYXNzKGYpIGlmIGluc3BlY3QuaXNjbGFzcyhmKSBlbHNlIF9SZW1vdGVGdW5jKGYpCgoKZGVmIGluaXQoKmEsICoqa3cpOgogICAgcGFzcwoKCmRlZiBnZXQob2lkcyk6CiAgICBpZiBpc2luc3RhbmNlKG9pZHMsIGxpc3QpOgogICAgICAgIHJldHVybiBbX3Vud3JhcChvKSBmb3IgbyBpbiBvaWRzXQogICAgcmV0dXJuIF91bndyYXAob2lkcykKCgpkZWYgcHV0KHZhbCk6CiAgICByZXR1cm4gX0Z1dHVyZSh2YWwpCgoKZGVmIHdhaXQob2lkcywgbnVtX3JldHVybnM9MSwgdGltZW91dD1Ob25lKToKICAgIGlmIG5vdCBpc2luc3RhbmNlKG9pZHMsIGxpc3QpOgogICAgICAgIG9pZHMgPSBbb2lkc10KICAgIHJldHVybiBvaWRzWzpudW1fcmV0dXJuc10sIG9pZHNbbnVtX3JldHVybnM6XQoKCmNsYXNzIF9Xb3JrZXIob2JqZWN0KToKICAgIGRlZiBjbGVhbnVwKHNlbGYpOgogICAgICAgIHBhc3MKCgp3b3JrZXIgPSBfV29ya2VyKCkKCgpjbGFzcyBfRXhwZXJpbWVudGFsKG9iamVjdCk6CiAgICBjbGFzcyBUZW5zb3JGbG93VmFyaWFibGVzKG9iamVjdCk6CiAgICAgICAgZGVmIF9faW5pdF9fKHNlbGYsICphLCAqKmt3KToKICAgICAgICAgICAgcGFzcwogICAgICAgIGRlZiBnZXRfd2VpZ2h0cyhzZWxmKToKICAgICAgICAgICAgcmV0dXJuIHt9CiAgICAgICAgZGVmIHNldF93ZWlnaHRzKHNlbGYsIHcpOgogICAgICAgICAgICBwYXNzCgoKZXhwZXJpbWVudGFsID0gX0V4cGVyaW1lbnRhbCgpCgpQWVRIT05fTU9ERSA9IDAKV09SS0VSX01PREUgPSAxClNDUklQVF9NT0RFID0gMgpTSUxFTlRfTU9ERSA9IDMKCmdsb2JhbF9zdGF0ZSA9IE5vbmUK').decode())"

RUN python3 -c "import base64; open('/workspace/stubs/ray/local_scheduler.py','w').write(base64.b64decode('Y2xhc3MgT2JqZWN0SUQob2JqZWN0KToKICAgIGRlZiBfX2luaXRfXyhzZWxmLCAqYSwgKiprdyk6CiAgICAgICAgcGFzcwoKZGVmIF9jb25maWcoKmEsICoqa3cpOgogICAgcGFzcwoKY29tbW9uX2Vycm9yID0gTm9uZQo=').decode())"

RUN python3 -c "import base64, site; sc=base64.b64decode('aW1wb3J0IHN5cwoKc3lzLnBhdGguaW5zZXJ0KDAsICcvd29ya3NwYWNlL3N0dWJzJykKCgpjbGFzcyBfVEZPYmoob2JqZWN0KToKICAgIGRlZiBfX2dldGF0dHJfXyhzZWxmLCBuYW1lKToKICAgICAgICByZXR1cm4gX1RGT2JqKCkKICAgIGRlZiBfX2NhbGxfXyhzZWxmLCAqYSwgKiprdyk6CiAgICAgICAgcmV0dXJuIF9URk9iaigpCiAgICBkZWYgX19lbnRlcl9fKHNlbGYpOgogICAgICAgIHJldHVybiBzZWxmCiAgICBkZWYgX19leGl0X18oc2VsZiwgKmEpOgogICAgICAgIHBhc3MKICAgIGRlZiBfX2l0ZXJfXyhzZWxmKToKICAgICAgICByZXR1cm4gaXRlcihbXSkKICAgIGRlZiBfX2xlbl9fKHNlbGYpOgogICAgICAgIHJldHVybiAwCiAgICBkZWYgX19ib29sX18oc2VsZik6CiAgICAgICAgcmV0dXJuIFRydWUKCgpjbGFzcyBfVEZNb2QoX1RGT2JqKToKICAgIHBhc3MKCgpfdGYgPSBfVEZNb2QoKQpzeXMubW9kdWxlc1sidGVuc29yZmxvdyJdID0gX3RmCnN5cy5tb2R1bGVzWyJ0ZW5zb3JmbG93LnB5dGhvbiJdID0gX1RGTW9kKCkKc3lzLm1vZHVsZXNbInRlbnNvcmZsb3cucHl0aG9uLmRlYnVnIl0gPSBfVEZNb2QoKQpzeXMubW9kdWxlc1sidGVuc29yZmxvdy5weXRob24uY2xpZW50Il0gPSBfVEZNb2QoKQpzeXMubW9kdWxlc1sidGVuc29yZmxvdy5weXRob24uZnJhbWV3b3JrIl0gPSBfVEZNb2QoKQpzeXMubW9kdWxlc1sidGVuc29yZmxvdy5weXRob24ub3BzIl0gPSBfVEZNb2QoKQpzeXMubW9kdWxlc1sidGVuc29yZmxvdy5weXRob24udHJhaW5pbmciXSA9IF9URk1vZCgpCnN5cy5tb2R1bGVzWyJ0ZW5zb3JmbG93LnB5dGhvbi51dGlsIl0gPSBfVEZNb2QoKQpzeXMubW9kdWxlc1sidGVuc29yZmxvdy5jb250cmliIl0gPSBfVEZNb2QoKQpzeXMubW9kdWxlc1sidGVuc29yZmxvdy5jb250cmliLmZyYW1ld29yayJdID0gX1RGTW9kKCkKc3lzLm1vZHVsZXNbInB5YXJyb3ciXSA9IF9URk1vZCgpCgppbXBvcnQgcmF5Cg==').decode(); sp=site.getsitepackages()[0]; open(sp+'/sitecustomize.py','w').write(sc)"

RUN ln -sf /workspace/repo/python/ray/rllib /workspace/stubs/ray/rllib
RUN ln -sf /workspace/repo/python/ray/tune /workspace/stubs/ray/tune

ENV PYTHONPATH=/workspace/stubs:/workspace/repo/python:$PYTHONPATH

WORKDIR /workspace/repo

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
