FROM rust:1.83

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    clang libclang-dev pkg-config libssl-dev protobuf-compiler \
    && rm -rf /var/lib/apt/lists/*

RUN cat > /usr/local/bin/git << 'GITEOF'
#!/usr/bin/env python3
import sys, os, re, tempfile

REAL_GIT = '/usr/bin/git'

def filter_binary_patches(content):
    sections = re.split(r'(?m)(?=^diff --git )', content)
    result = []
    for section in sections:
        is_binary = bool(re.search(r'^Binary files .* differ$', section, re.MULTILINE))
        has_binary_data = 'GIT binary patch' in section
        if is_binary and not has_binary_data:
            continue
        result.append(section)
    return ''.join(result)

if len(sys.argv) > 1 and sys.argv[1] == 'apply':
    new_args = [REAL_GIT, 'apply']
    for arg in sys.argv[2:]:
        if arg.startswith('-'):
            new_args.append(arg)
        elif os.path.isfile(arg):
            with open(arg, errors='replace') as f:
                content = f.read()
            filtered = filter_binary_patches(content)
            tmp = tempfile.NamedTemporaryFile('w', suffix='.patch', delete=False)
            tmp.write(filtered)
            tmp.close()
            new_args.append(tmp.name)
        else:
            new_args.append(arg)
    os.execv(REAL_GIT, new_args)
else:
    os.execv(REAL_GIT, [REAL_GIT] + sys.argv[1:])
GITEOF

RUN chmod +x /usr/local/bin/git

RUN curl -LsSf https://get.nexte.st/latest/linux | tar zxf - -C /usr/local/cargo/bin

WORKDIR /workspace

RUN /usr/bin/git init /workspace/repo && cd /workspace/repo && \
    /usr/bin/git remote add origin https://github.com/fedimint/fedimint.git && \
    /usr/bin/git fetch --depth 30 origin df6278bfcdcab3880cadb79757390b0175f4b167 && \
    /usr/bin/git checkout --detach FETCH_HEAD && \
    /usr/bin/git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    /usr/bin/git remote remove origin && \
    /usr/bin/git tag -l | xargs -r /usr/bin/git tag -d && \
    /usr/bin/git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 /usr/bin/git update-ref -d && \
    /usr/bin/git reflog expire --expire=now --all && \
    /usr/bin/git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN cargo fetch

RUN cargo build --tests --package fedimint-wallet-tests || true

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

# >>> offline-cargo-bake (new4261) >>>
# Warm $CARGO_HOME for the EXACT post-golden+test dependency graph, then revert
# the working tree so only the crate cache is baked (never the solution).
RUN printf %s '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' | base64 -d | gzip -d > /tmp/__golden.patch
RUN printf %s 'ZGlmZiAtLWdpdCBhL21vZHVsZXMvZmVkaW1pbnQtd2FsbGV0LXRlc3RzL3Rlc3RzL3Rlc3RzLnJzIGIvbW9kdWxlcy9mZWRpbWludC13YWxsZXQtdGVzdHMvdGVzdHMvdGVzdHMucnMKaW5kZXggM2ZjMTQwYjczNjIuLjA2MzQ1NmFiYTUwIDEwMDY0NAotLS0gYS9tb2R1bGVzL2ZlZGltaW50LXdhbGxldC10ZXN0cy90ZXN0cy90ZXN0cy5ycworKysgYi9tb2R1bGVzL2ZlZGltaW50LXdhbGxldC10ZXN0cy90ZXN0cy90ZXN0cy5ycwpAQCAtMTQ4Myw2ICsxNDgzLDcgQEAgYXN5bmMgZm4gdGVzdF9jbGllbnRfZGJfbWlncmF0aW9ucygpIC0+IGFueWhvdzo6UmVzdWx0PCgpPiB7CiAgICAgICAgICAgICAgICAgICAgICAgICBjbGllbnRfZGI6OkRiS2V5UHJlZml4OjpDbGFpbWVkUGVnSW4gPT4ge30KICAgICAgICAgICAgICAgICAgICAgICAgIGNsaWVudF9kYjo6RGJLZXlQcmVmaXg6OlJlY292ZXJ5RmluYWxpemVkID0+IHt9CiAgICAgICAgICAgICAgICAgICAgICAgICBjbGllbnRfZGI6OkRiS2V5UHJlZml4OjpSZWNvdmVyeVN0YXRlID0+IHt9CisgICAgICAgICAgICAgICAgICAgICAgICBjbGllbnRfZGI6OkRiS2V5UHJlZml4OjpTdXBwb3J0c1NhZmVEZXBvc2l0ID0+IHt9CiAgICAgICAgICAgICAgICAgICAgICAgICBjbGllbnRfZGI6OkRiS2V5UHJlZml4OjpFeHRlcm5hbFJlc2VydmVkU3RhcnQKICAgICAgICAgICAgICAgICAgICAgICAgIHwgY2xpZW50X2RiOjpEYktleVByZWZpeDo6Q29yZUludGVybmFsUmVzZXJ2ZWRTdGFydAogICAgICAgICAgICAgICAgICAgICAgICAgfCBjbGllbnRfZGI6OkRiS2V5UHJlZml4OjpDb3JlSW50ZXJuYWxSZXNlcnZlZEVuZCA9PiB7fQo=' | base64 -d > /tmp/__test.patch
RUN set -u; cd /workspace/repo; \
    apply_one() { pf="$1"; [ -s "$pf" ] || return 0; \
      patch -p1 -i "$pf" 2>/dev/null && return 0; \
      git apply --3way --ignore-whitespace "$pf" 2>/dev/null && return 0; \
      git apply --ignore-whitespace --whitespace=nowarn "$pf" 2>/dev/null && return 0; \
      patch -p1 --fuzz=5 -i "$pf" 2>/dev/null && return 0; \
      git ls-files -z 2>/dev/null | xargs -0 -r sed -i 's/\r$//' 2>/dev/null; \
      git apply --3way --ignore-whitespace "$pf" 2>/dev/null && return 0; \
      patch -p1 --fuzz=5 -i "$pf" 2>/dev/null && return 0; \
      return 0; }; \
    apply_one /tmp/__golden.patch; \
    apply_one /tmp/__test.patch; \
    (cargo fetch 2>&1 | tail -3 || true); \
    (cargo build --tests 2>&1 | tail -5 || true); \
    (cargo test --no-run 2>&1 | tail -5 || true); \
    (cargo nextest run --no-run 2>&1 | tail -5 || true); \
    git checkout -- . 2>/dev/null || true; \
    git clean -fdq 2>/dev/null || true; \
    rm -f /tmp/__golden.patch /tmp/__test.patch
ENV CARGO_NET_OFFLINE=true
# <<< offline-cargo-bake (new4261) <<<

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
