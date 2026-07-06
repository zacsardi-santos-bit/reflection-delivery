FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

ENV GOPATH=/go
ENV GO111MODULE=off

RUN mkdir -p /go/src/github.com/zalando-incubator

WORKDIR /go/src/github.com/zalando-incubator

RUN git init /go/src/github.com/zalando-incubator/kube-ingress-aws-controller && cd /go/src/github.com/zalando-incubator/kube-ingress-aws-controller && \
    git remote add origin https://github.com/zalando-incubator/kube-ingress-aws-controller.git && \
    git fetch --depth 30 origin 818b9f3f9ba8cb3d65b9b10be769ba43870debe0 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /go/src/github.com/zalando-incubator/kube-ingress-aws-controller

RUN mkdir -p /go/src/github.com/aws && \
    git clone https://github.com/aws/aws-sdk-go /go/src/github.com/aws/aws-sdk-go && \
    cd /go/src/github.com/aws/aws-sdk-go && git checkout 96358b8282b1a3aa66836d2f2fe66216cc419668

RUN mkdir -p /go/src/github.com/jmespath && \
    git clone https://github.com/jmespath/go-jmespath /go/src/github.com/jmespath/go-jmespath && \
    cd /go/src/github.com/jmespath/go-jmespath && git checkout bd40a432e4c76585ef6b72d3fd96fb9b6dc7b68d

RUN mkdir -p /go/src/github.com/go-ini && \
    git clone https://github.com/go-ini/ini /go/src/github.com/go-ini/ini && \
    cd /go/src/github.com/go-ini/ini && git checkout e7fea39b01aea8d5671f6858f0532f56e8bff3a5

RUN mkdir -p /go/src/github.com/golang && \
    git clone https://github.com/golang/protobuf /go/src/github.com/golang/protobuf && \
    cd /go/src/github.com/golang/protobuf && git checkout 8616e8ee5e20a1704615e6c8d7afcdac06087a67

RUN mkdir -p /go/src/github.com/beorn7 && \
    git clone https://github.com/beorn7/perks /go/src/github.com/beorn7/perks && \
    cd /go/src/github.com/beorn7/perks && git checkout 4c0e84591b9aa9e6dcfdf3e020114cd81f89d5f9

RUN mkdir -p /go/src/github.com/matttproud && \
    git clone https://github.com/matttproud/golang_protobuf_extensions /go/src/github.com/matttproud/golang_protobuf_extensions && \
    cd /go/src/github.com/matttproud/golang_protobuf_extensions && git checkout fc2b8d3a73c4867e51861bbdd5ae3c1f0869dd6a

RUN mkdir -p /go/src/github.com/prometheus && \
    git clone https://github.com/prometheus/client_model /go/src/github.com/prometheus/client_model && \
    cd /go/src/github.com/prometheus/client_model && git checkout 6f3806018612930941127f2a7c6c453ba2c527d2

RUN git clone https://github.com/prometheus/common /go/src/github.com/prometheus/common && \
    cd /go/src/github.com/prometheus/common && git checkout ffe929a3f4c4faeaa10f2b9535c2b1be3ad15650

RUN git clone https://github.com/prometheus/procfs /go/src/github.com/prometheus/procfs && \
    cd /go/src/github.com/prometheus/procfs && git checkout 454a56f35412459b5e684fd5ec0f9211b94f002a

RUN git clone https://github.com/prometheus/client_golang /go/src/github.com/prometheus/client_golang && \
    cd /go/src/github.com/prometheus/client_golang && git checkout c5b7fccd204277076155f10851dad72b76a49317

RUN mkdir -p /go/src/github.com/linki && \
    git clone https://github.com/linki/instrumented_http /go/src/github.com/linki/instrumented_http && \
    cd /go/src/github.com/linki/instrumented_http && git checkout 508103cfb3b315fa9752b5bcd4fb2d97bbc26d89

RUN mkdir -p /go/src/github.com/munnerz && \
    git clone https://github.com/munnerz/goautoneg /go/src/github.com/munnerz/goautoneg && \
    mkdir -p /go/src/bitbucket.org/ww && \
    cp -r /go/src/github.com/munnerz/goautoneg /go/src/bitbucket.org/ww/goautoneg

RUN mkdir -p /workspace && ln -s /go/src/github.com/zalando-incubator/kube-ingress-aws-controller /workspace/repo

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
