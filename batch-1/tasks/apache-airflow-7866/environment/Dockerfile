FROM python:3.8-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential gcc \
    libssl-dev libffi-dev python3-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin 2a98a61f596fa463f921aa4203a3b2019e2ac3a2 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel 2>&1 | tail -5

RUN pip install "markupsafe==1.1.1" "jinja2==2.10.3" 2>&1 | tail -5

RUN pip install \
    "alembic>=1.2,<2.0" \
    "argcomplete~=1.10" \
    "attrs~=19.3" \
    "cached_property~=1.5" \
    "cattrs~=1.0" \
    "colorlog==4.0.2" \
    "croniter>=0.3.17,<0.4" \
    "cryptography>=0.9.3" \
    "dill>=0.2.2,<0.4" \
    "flask>=1.1.0,<2.0" \
    "flask-appbuilder~=2.2" \
    "flask-caching>=1.3.3,<1.4.0" \
    "flask-login>=0.3,<0.5" \
    "flask-swagger==0.2.13" \
    "flask-wtf>=0.14.2,<0.15" \
    "graphviz>=0.12" \
    "gunicorn>=19.5.0,<20.0" \
    "iso8601>=0.1.12" \
    "jinja2>=2.10.1,<2.11.0" \
    "jsonschema~=3.0" \
    "lazy_object_proxy~=1.3" \
    "lockfile>=0.12.2" \
    "pandas>=0.17.1,<1.0.0" \
    "pendulum==1.4.4" \
    "psutil>=4.2.0,<6.0.0" \
    "pygments>=2.0.1,<3.0" \
    "python-daemon>=2.1.1,<2.2" \
    "python-dateutil>=2.3,<3" \
    "requests>=2.20.0,<3" \
    "sqlalchemy~=1.3" \
    "sqlalchemy_jsonfield~=0.9" \
    "tabulate>=0.7.5,<0.9" \
    "tenacity==4.12.0" \
    "termcolor==1.1.0" \
    "text-unidecode==1.2" \
    "tzlocal>=1.4,<2.0.0" \
    "werkzeug<1.0.0" \
    2>&1 | tail -10

RUN pip install \
    "google-api-python-client>=1.6.0,<2.0.0" \
    "google-auth>=1.0.0,<2.0.0" \
    "google-auth-httplib2>=0.0.1" \
    "google-cloud-storage>=1.16,<2.0.0" \
    "googleapis-common-protos>=1.51.0" \
    "grpcio>=1.27.2" \
    "google-api-core>=1.16.0" \
    2>&1 | tail -10

RUN pip install \
    "mock==3.0.5" \
    "pytest>=5.0,<6.0" \
    "pytest-cov" \
    "freezegun" \
    "parameterized" \
    2>&1 | tail -10

RUN pip install -e . 2>&1 | tail -10 || true

ENV PYTHONPATH=/workspace/repo:/workspace/repo/tests:$PYTHONPATH
ENV SKIP_INIT_DB=true
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
