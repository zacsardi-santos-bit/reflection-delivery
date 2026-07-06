FROM python:3.11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN pip install poetry

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/tortoise/tortoise-orm.git && \
    git fetch --depth 30 origin 46e3aefda15e90e23e22b76f62ae1c6f783ce7a7 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN poetry config virtualenvs.create false
RUN poetry install --without dev --no-interaction 2>&1 | tail -10 || true
RUN pip install pytest pytest-asyncio pytest-xdist aiosqlite "pydantic>=2.0,!=2.7.0"

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

# --- offline-harbor PY: wheelhouse for inline `pip install`/build at test time (build isolation) ---
# 1) Stage common PEP517 build backends so test-time build isolation resolves offline.
# 2) Stage the FULL already-resolved runtime env into /wheels so `pip install -e .`
#    (which re-resolves deps) is satisfied offline.
RUN PYBIN="$(command -v python3 || command -v python)"; \
    "$PYBIN" -m pip download -d /wheels \
      pip setuptools wheel "setuptools_scm[toml]" setuptools-rust \
      hatchling hatch-vcs hatch-fancy-pypi-readme editables pathspec pluggy trove-classifiers \
      poetry-core flit-core flit_scm pdm-backend pdm-pep517 \
      Cython pybind11 maturin scikit-build-core calver packaging tomli typing_extensions \
      2>/dev/null || true; \
    ("$PYBIN" -m pip freeze 2>/dev/null | grep -vE '^-e |@ file://|^#' > /tmp/_frozen.txt || true); \
    "$PYBIN" -m pip download -d /wheels -r /tmp/_frozen.txt 2>/dev/null || true
ENV PIP_FIND_LINKS=/wheels
ENV PIP_NO_INDEX=1
# Rust/cargo offline (qiskit/pydantic-core/etc rebuild native ext at test time)
ENV CARGO_NET_OFFLINE=true
# --- end offline-harbor PY wheelhouse ---

# --- offline-harbor PY-EXTRA: satisfy editable install's own version constraints offline ---
# Build-time poetry pinned pypika-tortoise 0.4.0, but the repo's pyproject requires >=0.5.0,<0.6.0;
# the test-time `pip install -e .` re-resolves and fails offline. Install+stage the correct version.
RUN PYBIN="$(command -v python3 || command -v python)"; unset PIP_NO_INDEX; \
    "$PYBIN" -m pip install --index-url https://pypi.org/simple "pypika-tortoise>=0.5.0,<0.6.0" 2>&1 | tail -3 || true; \
    "$PYBIN" -m pip download --index-url https://pypi.org/simple -d /wheels "pypika-tortoise>=0.5.0,<0.6.0" 2>/dev/null || true
# --- end offline-harbor PY-EXTRA ---

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
