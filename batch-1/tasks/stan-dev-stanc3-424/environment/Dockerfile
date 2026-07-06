FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    m4 unzip pkg-config libpcre3-dev rsync \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL "https://github.com/ocaml/opam/releases/download/2.1.5/opam-2.1.5-x86_64-linux" \
    -o /usr/local/bin/opam && chmod +x /usr/local/bin/opam

RUN opam init --disable-sandboxing --bare -y

RUN opam switch create 4.07.0 \
    --packages=ocaml-base-compiler.4.07.0 \
    --repos=default=https://opam.ocaml.org,archive=git+https://github.com/ocaml/opam-repository-archive.git

RUN opam exec -- opam pin add -y dune 1.11.4 && \
    opam exec -- opam install -y core_kernel.v0.11.1 menhir.20181113 ppx_deriving.4.2.1 fmt.0.8.5 yojson.1.7.0 re

ENV PATH="/root/.opam/4.07.0/bin:$PATH"
ENV CAML_LD_LIBRARY_PATH="/root/.opam/4.07.0/lib/stublibs:/root/.opam/4.07.0/lib/ocaml/stublibs:/root/.opam/4.07.0/lib/ocaml"
ENV OPAM_SWITCH_PREFIX="/root/.opam/4.07.0"
ENV OCAML_TOPLEVEL_PATH="/root/.opam/4.07.0/lib/toplevel"

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/stan-dev/stanc3.git && \
    git fetch --depth 30 origin 2e79fb12ef08259b3cbfc8802ae3e22405396704 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN dune build src/stanc/stanc.exe

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
