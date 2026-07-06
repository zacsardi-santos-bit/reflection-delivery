FROM python:3.8-bullseye

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    wget unzip python2 openjdk-11-jre-headless redis-server \
    && rm -rf /var/lib/apt/lists/*

RUN curl -sL https://deb.nodesource.com/setup_16.x | bash - && apt-get install -y nodejs

RUN pip install --upgrade pip==21.2.3
RUN pip install pip-tools==6.6.2 setuptools==58.5.3 wheel==0.35.0 pytest

WORKDIR /app/vm_deps

RUN wget -q -O /app/vm_deps/gcloud-sdk.tar.gz https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-364.0.0-linux-x86_64.tar.gz && \
    tar -xzf gcloud-sdk.tar.gz && \
    ./google-cloud-sdk/install.sh --quiet && \
    ./google-cloud-sdk/bin/gcloud components install beta cloud-datastore-emulator app-engine-python app-engine-python-extras --quiet && \
    rm gcloud-sdk.tar.gz

ENV PATH="/app/vm_deps/google-cloud-sdk/bin:$PATH"
ENV OPPIA_IS_DOCKERIZED="true"
ENV DATASTORE_EMULATOR_HOST="localhost:8089"
ENV DATASTORE_EMULATOR_HOST_PATH="localhost:8089/datastore"
ENV DATASTORE_DATASET="dev-project-id"
ENV DATASTORE_HOST="http://localhost:8089"
ENV DATASTORE_PROJECT_ID="dev-project-id"
ENV DATASTORE_USE_PROJECT_ID_AS_APP_ID="true"
ENV GOOGLE_CLOUD_PROJECT="dev-project-id"
ENV CLOUDSDK_CORE_PROJECT="dev-project-id"
ENV APPLICATION_ID="dev-project-id"

ENV BUF_LINUX_FILES="buf-Linux-x86_64 protoc-gen-buf-check-lint-Linux-x86_64 protoc-gen-buf-check-breaking-Linux-x86_64"
ENV PROTOC_LINUX_FILE="protoc-3.13.0-linux-x86_64.zip"
ENV BUF_DIR="/app/vm_deps/buf-0.29.0"
ENV PROTOC_DIR="/app/vm_deps/buf-0.29.0/protoc"
ENV BUF_BASE_URL="https://github.com/bufbuild/buf/releases/download/v0.29.0/"
ENV PROTOC_URL="https://github.com/protocolbuffers/protobuf/releases/download/v3.13.0"

RUN wget -P $BUF_DIR $PROTOC_URL/$PROTOC_LINUX_FILE
RUN unzip $BUF_DIR/$PROTOC_LINUX_FILE -d $PROTOC_DIR && rm $BUF_DIR/$PROTOC_LINUX_FILE
RUN chmod -R 744 $BUF_DIR && chmod -R 744 $PROTOC_DIR

WORKDIR /tmp/protoc-npm
RUN npm init -y && npm install protoc-gen-ts@0.3.4 google-protobuf@3.13.0 typescript@4.1.6

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/oppia/oppia.git && \
    git fetch --depth 30 origin dcd3639204ed2039aa0b73dce2365b74df1d3040 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --no-deps -r requirements.txt -t /workspace/repo/third_party/python_libs 2>/dev/null || pip install -r requirements.txt -t /workspace/repo/third_party/python_libs --no-deps --no-warn-script-location
RUN pip install --no-deps -r requirements_dev.txt 2>/dev/null || pip install -r requirements_dev.txt --no-deps --no-warn-script-location

RUN python -m scripts.install_dependencies_json_packages

ENV PROTOC_FILES_PATH="/workspace/repo/third_party/oppia-ml-proto-0.0.0"

RUN mkdir /workspace/repo/proto_files
RUN $PROTOC_DIR/bin/protoc \
    --plugin=protoc-gen-ts=/tmp/protoc-npm/node_modules/.bin/protoc-gen-ts \
    --ts_out=/workspace/repo/extensions/classifiers/proto/ \
    --js_out=import_style=commonjs,binary:/workspace/repo/extensions/classifiers/proto/ \
    --python_out=/workspace/repo/proto_files \
    --proto_path=$PROTOC_FILES_PATH \
    $PROTOC_FILES_PATH/*.proto

RUN sed -i 's/import text_classifier_pb2 as text__classifier__pb2/from . import text_classifier_pb2 as text__classifier__pb2/' /workspace/repo/proto_files/training_job_response_payload_pb2.py

ENV PYTHONPATH="/workspace/repo:/workspace/repo/third_party/python_libs:$PYTHONPATH"

RUN git config --global --add safe.directory /workspace/repo

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
