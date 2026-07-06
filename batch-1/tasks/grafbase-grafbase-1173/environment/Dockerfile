FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential pkg-config libssl-dev \
    && rm -rf /var/lib/apt/lists/*

RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --default-toolchain beta --profile default
ENV PATH="/root/.cargo/bin:${PATH}"

RUN cargo install cargo-nextest --locked 2>&1 | tail -5

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/grafbase/grafbase.git && \
    git fetch --depth 30 origin 032901e24ba9b620c508cdf77d51517382200f57 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN mkdir -p /workspace/testenv/.config /workspace/testenv/graphql-schema-diff/src /workspace/testenv/graphql-schema-diff/tests

RUN printf '[workspace]\nresolver = "2"\nmembers = ["graphql-schema-diff"]\n\n[workspace.package]\nversion = "0.1.0"\nedition = "2021"\nlicense = "Apache-2.0"\nhomepage = "https://grafbase.com"\nkeywords = ["grafbase"]\nrepository = "https://github.com/grafbase/grafbase"\n\n[workspace.dependencies]\nserde = { version = "1", features = ["derive"] }\nserde_json = "1"\n\n[workspace.lints.rust]\nnonstandard-style = "deny"\nrust-2018-idioms = "deny"\nunused-crate-dependencies = "deny"\n\n[workspace.lints.clippy]\nall = "deny"\npanic = "deny"\nbool-to-int-with-if = "allow"\ncast-possible-truncation = "allow"\ndefault-trait-access = "allow"\nderive-partial-eq-without-eq = "allow"\ndoc-markdown = "allow"\nenum-glob-use = "allow"\nimplicit-hasher = "allow"\nitems-after-statements = "allow"\ninto_iter_without_iter = "allow"\niter_without_into_iter = "allow"\nlarge-digit-groups = "allow"\nlet-underscore-untyped = "allow"\nlet-with-type-underscore = "allow"\nmanual-assert = "allow"\nmap-unwrap-or = "allow"\nmatch-wildcard-for-single-variants = "allow"\nmissing-errors-doc = "allow"\nmissing-panics-doc = "allow"\nmodule-name-repetitions = "allow"\nmust-use-candidate = "allow"\nneedless-pass-by-value = "allow"\nredundant-closure-for-method-calls = "allow"\nsingle-match-else = "allow"\nstruct-excessive-bools = "allow"\nstruct-field-names = "allow"\ntoo-many-lines = "allow"\nunnecessary-fallible-conversions = "allow"\nunused-async = "allow"\nunused-self = "allow"\nwildcard-imports = "allow"\n\n[patch.crates-io]\nasync-graphql = { git = "https://github.com/async-graphql/async-graphql", rev = "44ae98335413a11e1acbc6c48135f452996e8a0b" }\nasync-graphql-axum = { git = "https://github.com/async-graphql/async-graphql", rev = "44ae98335413a11e1acbc6c48135f452996e8a0b" }\nasync-graphql-parser = { git = "https://github.com/async-graphql/async-graphql", rev = "44ae98335413a11e1acbc6c48135f452996e8a0b" }\nasync-graphql-value = { git = "https://github.com/async-graphql/async-graphql", rev = "44ae98335413a11e1acbc6c48135f452996e8a0b" }\n' > /workspace/testenv/Cargo.toml

RUN printf '[package]\nname = "graphql-schema-diff"\nversion = "0.1.0"\nedition.workspace = true\nlicense.workspace = true\nhomepage.workspace = true\nkeywords.workspace = true\nrepository.workspace = true\n\n[dependencies]\nasync-graphql-parser = "6"\nasync-graphql-value = "6"\nserde = { workspace = true, optional = true, features = ["derive"] }\n\n[features]\ndefault = ["serde"]\n\n[dev-dependencies]\ndatatest-stable = "0.2.3"\nsimilar = "2"\nmiette = { version = "5.10.0", features = ["fancy"] }\nserde_json.workspace = true\n\n[[test]]\nname = "diff_tests"\nharness = false\n' > /workspace/testenv/graphql-schema-diff/Cargo.toml && \
    printf 'pub fn placeholder() {}\n' > /workspace/testenv/graphql-schema-diff/src/lib.rs && \
    printf '#![allow(unused_crate_dependencies)]\nuse std::path::Path;\nfn run_test(_case: &Path) -> datatest_stable::Result<()> { Ok(()) }\ndatatest_stable::harness! { run_test, "./tests", r"^.*\\.graphql$", }\n' > /workspace/testenv/graphql-schema-diff/tests/diff_tests.rs

RUN cd /workspace/testenv && cargo build --tests 2>&1 | tail -10

RUN printf '[profile.ci]\nslow-timeout = { period = "1m", terminate-after = 2 }\nretries = 2\nfailure-output = "immediate-final"\nfail-fast = false\n' > /workspace/testenv/.config/nextest.toml

ENV CARGO_NET_OFFLINE=true

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
