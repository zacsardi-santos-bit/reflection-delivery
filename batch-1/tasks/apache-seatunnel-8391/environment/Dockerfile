FROM eclipse-temurin:11

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential maven \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/seatunnel.git && \
    git fetch --depth 30 origin 757641bada16cf59400723d0720124abee3d1e26 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN mvn install -DskipTests \
    -pl seatunnel-connectors-v2/connector-kafka,seatunnel-formats/seatunnel-format-avro,seatunnel-formats/seatunnel-format-json \
    --also-make \
    -Dcheckstyle.skip=true \
    -Dlicense.skip=true
# OFFLINE_WARM_MARKER (baked deps at build for offline test) (OFFLINE_WARM_V3)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIG12biBpbnN0YWxsIC1Ec2tpcFRlc3RzIC1wbCBzZWF0dW5uZWwtY29ubmVjdG9ycy12Mi9jb25uZWN0b3Ita2Fma2Esc2VhdHVubmVsLWZvcm1hdHMvc2VhdHVubmVsLWZvcm1hdC1hdnJvLHNlYXR1bm5lbC1mb3JtYXRzL3NlYXR1bm5lbC1mb3JtYXQtanNvbiAtLWFsc28tbWFrZSAtRGNoZWNrc3R5bGUuc2tpcD10cnVlIC1EbGljZW5zZS5za2lwPXRydWUgLURzcG90bGVzcy5jaGVjay5za2lwPXRydWUgJiYgbXZuIHRlc3QgLXBsIHNlYXR1bm5lbC1jb25uZWN0b3JzLXYyL2Nvbm5lY3Rvci1rYWZrYSxzZWF0dW5uZWwtZm9ybWF0cy9zZWF0dW5uZWwtZm9ybWF0LWF2cm8sc2VhdHVubmVsLWZvcm1hdHMvc2VhdHVubmVsLWZvcm1hdC1qc29uIC1EdGVzdD0iS2Fma2FTb3VyY2VDb25maWdUZXN0LEF2cm9Db252ZXJ0ZXJUZXN0LEF2cm9TZXJpYWxpemF0aW9uU2NoZW1hVGVzdCxEZWJleml1bUpzb25EZXNlcmlhbGl6YXRpb25TY2hlbWFEaXNwYXRjaGVyVGVzdCxEZWJleml1bUpzb25TZXJEZVNjaGVtYVRlc3QiIC1EZmFpbElmTm9UZXN0cz1mYWxzZSAtRGNoZWNrc3R5bGUuc2tpcD10cnVlIC1EbGljZW5zZS5za2lwPXRydWU= | base64 -d > /workspace/.offline_warm.sh && \
    (timeout 3000 bash /workspace/.offline_warm.sh 2>&1 | tail -25 || true)

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
