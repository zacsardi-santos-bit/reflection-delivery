FROM python:3.8

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libssl-dev libffi-dev libsasl2-dev libldap2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/airflow.git && \
    git fetch --depth 30 origin d8d48c2d8571c632b23e7cb6839902d9164e849d && \
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

RUN pip install \
    "marshmallow==2.21.0" \
    "marshmallow-sqlalchemy==0.23.1" \
    "marshmallow-enum==1.5.1" \
    "Flask==1.1.2" \
    "Werkzeug==0.16.1" \
    "Jinja2==2.10.3" \
    "MarkupSafe==1.1.1" \
    "itsdangerous==1.1.0" \
    "click==6.7" \
    "SQLAlchemy==1.3.17" \
    "alembic==1.4.2" \
    "cryptography==2.9.2" \
    "pendulum==2.1.0" \
    "python-dateutil==2.8.1" \
    "pytz==2020.1" \
    "tzlocal==1.5.1" \
    "connexion[swagger-ui,flask]==2.7.0" \
    "apispec==1.3.3" \
    "jsonschema==3.2.0" \
    "Flask-AppBuilder==2.3.4" \
    "Flask-SQLAlchemy==2.4.3" \
    "Flask-Login==0.4.1" \
    "Flask-WTF==0.14.3" \
    "Flask-JWT-Extended==3.24.1" \
    "Flask-Caching==1.3.3" \
    "flask-swagger==0.2.13" \
    "WTForms==2.3.1" \
    "attrs==19.3.0" \
    "cattrs==1.0.0" \
    "cached-property==1.5.1" \
    "colorlog==4.0.2"

RUN pip install -e . > /tmp/pip_airflow.log 2>&1 || (tail -50 /tmp/pip_airflow.log && exit 1)

RUN pip install "parameterized==0.7.4" "pytest==5.4.3" "pytest-cov" "freezegun" "pytest-timeout"

ENV AIRFLOW_HOME=/workspace/airflow_home
ENV AIRFLOW__CORE__UNIT_TEST_MODE=True
ENV AIRFLOW__CORE__LOAD_EXAMPLES=False

RUN mkdir -p /workspace/airflow_home && airflow db init

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
