FROM python:3.7

RUN apt-get update && apt-get install -y \
    git curl ca-certificates build-essential libpq-dev libssl-dev libffi-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin b31773cff98241572819fea83a25e2edd58078be && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel

RUN sed -i \
    -e "s/'jinja2>=2.10.1, <2.11.0'/'jinja2>=2.10.1'/g" \
    -e "s/'dill>=0.2.2, <0.3'/'dill>=0.2.2'/g" \
    -e "s/'croniter>=0.3.17, <0.4'/'croniter>=0.3.17'/g" \
    -e "s/'tzlocal>=1.4,<2.0.0'/'tzlocal>=1.4'/g" \
    -e "s/'flask-caching>=1.3.3, <1.4.0'/'flask-caching>=1.3.3'/g" \
    -e "s/'python-daemon>=2.1.1, <2.2'/'python-daemon>=2.1.1'/g" \
    -e "s/'flask-swagger==0.2.13'/'flask-swagger>=0.2.13'/g" \
    -e "s/'marshmallow-sqlalchemy>=0.16.1, <0.19.0'/'marshmallow-sqlalchemy>=0.16.1'/g" \
    -e "s/'pandas>=0.17.1, <1.0.0'/'pandas>=0.17.1'/g" \
    -e "s/'flask-appbuilder>=1.12.5, <2.0.0'/'flask-appbuilder>=1.12.5'/g" \
    setup.py

RUN pip install -e . --no-deps

RUN pip install \
    "sqlalchemy>=1.3,<1.4" \
    "alembic>=1.0,<2.0" \
    "flask>=1.1.0,<2.0" \
    "flask-login>=0.3,<0.5" \
    "markupsafe<2.0" \
    "jinja2>=2.10.1,<2.12" \
    "pendulum==1.4.4" \
    "python-dateutil>=2.3,<3" \
    "requests>=2.20.0,<3" \
    "croniter>=0.3.17" \
    "dill>=0.2.2" \
    "lazy_object_proxy>=1.3" \
    "tenacity==4.12.0" \
    "colorlog==4.0.2" \
    "tabulate>=0.7.5" \
    "funcsigs>=1.0.0" \
    "graphviz>=0.12" \
    "psutil>=4.2.0" \
    "text-unidecode>=1.2" \
    "termcolor>=1.1.0" \
    "gunicorn>=19.5.0" \
    "iso8601>=0.1.12" \
    "json-merge-patch>=0.2" \
    "cached_property>=1.5" \
    "argcomplete>=1.10" \
    "setproctitle>=1.1.8" \
    "tzlocal>=1.4" \
    "zope.deprecation>=4.0" \
    "marshmallow-sqlalchemy>=0.16.1" \
    "markdown>=2.5.2" \
    "pygments>=2.0.1" \
    "unicodecsv>=0.14.1" \
    "flask-wtf>=0.14.2" \
    "flask-swagger>=0.2.13" \
    "flask-caching>=1.3.3" \
    "flask-appbuilder>=1.12.5" \
    "python-daemon>=2.1.1" \
    "lockfile" \
    "WTForms"

RUN pip install \
    "google-auth>=1.0.0" \
    "google-auth-httplib2>=0.0.1" \
    "google-api-python-client>=1.6.0" \
    "httplib2>=0.9.2" \
    "google-cloud-container>=0.1.1,<1.0.0" \
    "google-cloud-core" \
    "kubernetes>=3.0.0" \
    "cryptography>=2.0.0" \
    "numpy>=1.15.0" \
    "pandas>=0.17.1"

RUN pip install parameterized pytest

RUN echo "" > /workspace/repo/tests/__init__.py

ENV AIRFLOW_CONN_GOOGLE_CLOUD_DEFAULT=google-cloud-platform://

ENV PYTHONPATH=/workspace/repo:$PYTHONPATH

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
