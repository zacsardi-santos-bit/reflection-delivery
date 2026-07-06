FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/sgl-project/sglang.git && \
    git fetch --depth 30 origin 839f7f2696f98db0af375b8ced62af4ebbe07efb && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN mkdir -p /workspace/stub_pkgs/nixl \
             /workspace/stub_pkgs/sglang/srt/mem_cache/storage

RUN printf '' > /workspace/stub_pkgs/nixl/__init__.py

RUN cat > /workspace/stub_pkgs/nixl/_api.py << 'NIXLEOF'
import os
import ctypes
import torch


class nixl_agent_config:
    def __init__(self, backends=None):
        self.backends = backends or []


class _XferDescs:
    def __init__(self, items, mem_type=None):
        self.items = items
        self.mem_type = mem_type


class _XferReq:
    def __init__(self, direction, host_descs, storage_descs):
        self.direction = direction
        self.host_descs = host_descs
        self.storage_descs = storage_descs


class nixl_agent:
    def __init__(self, name, config):
        self.name = name

    def get_plugin_list(self):
        return ["POSIX", "GDS", "GDS_MT"]

    def create_backend(self, plugin_name, initparams):
        pass

    def get_backend_params(self, backend_name):
        return {}

    def get_reg_descs(self, items, mem_type=None):
        return _XferDescs(items, mem_type)

    def register_memory(self, reg_descs):
        return reg_descs

    def get_xfer_descs(self, items, mem_type=None):
        return _XferDescs(items, mem_type)

    def initialize_xfer(self, direction, host_descs, storage_descs, agent_name):
        return _XferReq(direction, host_descs, storage_descs)

    def transfer(self, xfer_req):
        direction = xfer_req.direction
        host_items = xfer_req.host_descs.items if xfer_req.host_descs else []
        storage_items = xfer_req.storage_descs.items if xfer_req.storage_descs else []
        if direction == "WRITE":
            for host_item, storage_item in zip(host_items, storage_items):
                fd = storage_item[2]
                os.lseek(fd, 0, 0)
                if isinstance(host_item, torch.Tensor):
                    data = host_item.contiguous().view(torch.uint8).numpy().tobytes()
                    os.write(fd, data)
                elif isinstance(host_item, tuple):
                    addr, size = host_item[0], host_item[1]
                    data = (ctypes.c_char * size).from_address(addr).raw
                    os.write(fd, data)
        elif direction == "READ":
            for host_item, storage_item in zip(host_items, storage_items):
                fd = storage_item[2]
                os.lseek(fd, 0, 0)
                if isinstance(host_item, torch.Tensor):
                    nbytes = host_item.numel() * host_item.element_size()
                    data = os.read(fd, nbytes)
                    import numpy as np
                    arr = np.frombuffer(data, dtype=np.uint8).copy()
                    host_item.copy_(torch.from_numpy(arr).view(host_item.dtype).reshape(host_item.shape))
                elif isinstance(host_item, tuple):
                    addr, size = host_item[0], host_item[1]
                    data = os.read(fd, size)
                    ctypes.memmove(addr, data, len(data))
        return "DONE"

    def check_xfer_state(self, xfer_req):
        return "DONE"

    def release_xfer_handle(self, xfer_req):
        pass

    def query_memory(self, tuples, backend_name, mem_type=None):
        result = []
        for tup in tuples:
            file_path = tup[3]
            if os.path.exists(file_path) and os.path.getsize(file_path) > 0:
                result.append(True)
            else:
                result.append(None)
        return result
NIXLEOF

RUN printf '__path__.append("/workspace/repo/python/sglang")\n' \
    > /workspace/stub_pkgs/sglang/__init__.py

RUN printf 'class HostKVCache:\n    pass\n' \
    > /workspace/stub_pkgs/sglang/srt/mem_cache/memory_pool_host.py

RUN printf '__path__.append("/workspace/repo/python/sglang/srt/mem_cache/storage")\n' \
    > /workspace/stub_pkgs/sglang/srt/mem_cache/storage/__init__.py

RUN pip install torch --index-url https://download.pytorch.org/whl/cpu 2>&1 | tail -5 && \
    pip install pytest numpy 2>&1 | tail -5

ENV PYTHONPATH=/workspace/stub_pkgs:/workspace/repo/python


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
