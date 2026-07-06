FROM python:3.8

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/rapidsai/cuml.git && \
    git fetch --depth 30 origin 0d1014a217f0e6085f2cb2f9e54af79a056cb880 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --no-cache-dir \
    "dask==2022.5.0" \
    "distributed==2022.5.0" \
    toolz \
    pytest \
    scikit-learn \
    numpy

RUN python3 - <<'PEOF'
import site, os

code = '''import sys
import types

def _make_module(name):
    mod = types.ModuleType(name)
    sys.modules[name] = mod
    return mod

cupy = _make_module("cupy")
cupy_cuda = _make_module("cupy.cuda")
cupy.cuda = cupy_cuda
cupy_cuda.set_allocator = lambda x=None: None

class _SN:
    def synchronize(self):
        pass

class _S:
    null = _SN()

cupy_cuda.Stream = _S()
cupy.array = lambda *a, **kw: None
cupy.asarray = lambda *a, **kw: None
cupy.zeros = lambda *a, **kw: None
cupy.unique = lambda *a, **kw: None
cupy.asnumpy = lambda arr: arr
cupy.int32 = int
cupy.float32 = float
cupy.float64 = float

numba = _make_module("numba")
numba_cuda = _make_module("numba.cuda")
numba.cuda = numba_cuda

cuml = _make_module("cuml")
cuml.__path__ = ["/workspace/repo/python/cuml"]
cuml.__spec__ = None

cuml_utils = _make_module("cuml.utils")
cuml.utils = cuml_utils
cuml_utils.device_of_gpu_matrix = lambda m: 0
cuml_utils.rmm_cupy_ary = lambda f, *a, **kw: f(*a, **kw)
cuml_utils.with_cupy_rmm = lambda f: f

cuml_dask = _make_module("cuml.dask")
cuml.dask = cuml_dask
cuml_dask.__path__ = ["/workspace/repo/python/cuml/dask"]

cuml_dask_common = _make_module("cuml.dask.common")
cuml_dask.common = cuml_dask_common
cuml_dask_common.__path__ = ["/workspace/repo/python/cuml/dask/common"]
cuml_dask_common.__package__ = "cuml.dask.common"

dask_cuda = _make_module("dask_cuda")
dask_cuda_init = _make_module("dask_cuda.initialize")
dask_cuda.initialize = dask_cuda_init
dask_cuda_init.initialize = lambda **kw: None

class _FakeLCC:
    def __init__(self, *a, **kw):
        from dask.distributed import LocalCluster
        ck = {}
        if "scheduler_port" in kw:
            ck["scheduler_port"] = kw["scheduler_port"]
        self._c = LocalCluster(n_workers=1, threads_per_worker=1, **ck)

    def close(self):
        self._c.close()

    def __getattr__(self, n):
        return getattr(self._c, n)

dask_cuda.LocalCUDACluster = _FakeLCC
'''

sp = site.getsitepackages()[0]
path = os.path.join(sp, "sitecustomize.py")
with open(path, "w") as f:
    f.write(code)
print("Written sitecustomize.py to", path)
PEOF

ENV PYTHONPATH=/workspace/repo/python

WORKDIR /workspace

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

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
