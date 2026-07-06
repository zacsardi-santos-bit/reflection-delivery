FROM eclipse-temurin:21-jdk

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/apache/kafka.git && \
    git fetch --depth 30 origin 482694c9d364ecf496a683af4f7abb0d8fd76a46 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN chmod +x ./gradlew

RUN ./gradlew --version --no-daemon --no-watch-fs 2>&1 | tail -5

RUN ./gradlew :streams:dependencies --no-daemon --no-watch-fs -q 2>&1 | tail -10 || true

RUN ./gradlew :streams:testClasses --no-daemon --no-watch-fs 2>&1 | tail -20 || true


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

# --- offline-harbor(fx_09): bake gradle test-graph deps at BUILD time ---
RUN cd /workspace/repo && (./gradlew --version --no-daemon --no-watch-fs 2>&1 | tail -5 || true)
RUN echo Y2QgL3dvcmtzcGFjZS9yZXBvICYmIC4vZ3JhZGxldyA6c3RyZWFtczp0ZXN0IC0tdGVzdHMgIipTZXNzaW9uV2luZG93ZWRLU3RyZWFtSW1wbFRlc3QiIC0tdGVzdHMgIipLZXlWYWx1ZVN0b3JlTWF0ZXJpYWxpemVyVGVzdCIgLS10ZXN0cyAiKlNlc3Npb25TdG9yZU1hdGVyaWFsaXplclRlc3QiIC0tdGVzdHMgIipTbGlkaW5nV2luZG93U3RvcmVNYXRlcmlhbGl6ZXJUZXN0IiAtLXRlc3RzICIqQWJzdHJhY3RTZXNzaW9uQnl0ZXNTdG9yZVRlc3QiIC0tdGVzdHMgIipUaW1lT3JkZXJlZFNlc3Npb25TdG9yZVVwZ3JhZGVUZXN0IiAtLW5vLWRhZW1vbiAtLW5vLXdhdGNoLWZzOyBmaW5kIC93b3Jrc3BhY2UvcmVwby9zdHJlYW1zL2J1aWxkL3Rlc3QtcmVzdWx0cyAtbmFtZSAnVEVTVC0qLnhtbCcgLWV4ZWMgY2F0IHt9IFw7IDI+L2Rldi9udWxsIHx8IHRydWU= | base64 -d | bash > /tmp/warm.log 2>&1 || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true
# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` (the client
# verifier exec) keeps go/cargo/java/etc on PATH after /etc/profile resets it.
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
