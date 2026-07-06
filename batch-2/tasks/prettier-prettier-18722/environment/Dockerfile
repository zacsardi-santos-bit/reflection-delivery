FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential gnupg \
    && rm -rf /var/lib/apt/lists/*

RUN install -dm 755 /etc/apt/keyrings && \
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg && \
    echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_20.x nodistro main" > /etc/apt/sources.list.d/nodesource.list && \
    apt-get update && \
    apt-get install -y nodejs && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/prettier/prettier.git && \
    git fetch --depth 30 origin 375f3347b170034db0cc5091b01467a810b673a5 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN node .yarn/releases/yarn-4.12.0.cjs install --immutable

WORKDIR /workspace

# OFFLINE-NODE-BAKE bake PM cache/store for offline test-time install (build-time network)
# warm PM cache/store for golden-added deps WITHOUT destroying baked artifacts
RUN echo 'ZGlmZiAtLWdpdCBhL2NoYW5nZWxvZ191bnJlbGVhc2VkL2FuZ3VsYXIvMTg3MjIubWQgYi9jaGFuZ2Vsb2dfdW5yZWxlYXNlZC9hbmd1bGFyLzE4NzIyLm1kCm5ldyBmaWxlIG1vZGUgMTAwNjQ0CmluZGV4IDAwMDAwMDAwMC4uNjFlNjk1MzVlCi0tLSAvZGV2L251bGwKKysrIGIvY2hhbmdlbG9nX3VucmVsZWFzZWQvYW5ndWxhci8xODcyMi5tZApAQCAtMCwwICsxLDQxIEBACisjIyMjIFN1cHBvcnQgQW5ndWxhciB2MjEuMiAoIzE4NzIyLCAjMTkwMzQgYnkgQGZpc2tlcikKKworRXhoYXVzdGl2ZSB0eXBlY2hlY2tpbmcgd2l0aCBgQGRlZmF1bHQgbmV2ZXI7YAorCis8IS0tIHByZXR0aWVyLWlnbm9yZSAtLT4KK2BgYGh0bWwKKzwhLS0gSW5wdXQgLS0+CitAc3dpdGNoIChmb28pIHsKKyAgQGNhc2UgKDEpIHt9CisgIEBkZWZhdWx0IG5ldmVyOworfQorCis8IS0tIFByZXR0aWVyIHN0YWJsZSAtLT4KK1N5bnRheEVycm9yOiBJbmNvbXBsZXRlIGJsb2NrICJkZWZhdWx0IG5ldmVyIi4gSWYgeW91IG1lYW50IHRvIHdyaXRlIHRoZSBAIGNoYXJhY3RlciwgeW91IHNob3VsZCB1c2UgdGhlICImIzY0OyIgSFRNTCBlbnRpdHkgaW5zdGVhZC4gKDM6MykKKworPCEtLSBQcmV0dGllciBtYWluIC0tPgorQHN3aXRjaCAoZm9vKSB7CisgIEBjYXNlICgxKSB7fQorICBAZGVmYXVsdCBuZXZlcjsKK30KK2BgYAorCitgYXJyb3cgZnVuY3Rpb25gIGFuZCBgaW5zdGFuY2VvZmAgZXhwcmVzc2lvbnMuCisKKzwhLS0gcHJldHRpZXItaWdub3JlIC0tPgorYGBgaHRtbAorPCEtLSBJbnB1dCAtLT4KK0BsZXQgZm4gPSAoYSkgPT4gICAgICAgIGE/ICAgIDE6MjsKKwore3sgZm4gKCBhICAgICAgICAgaW5zdGFuY2VvZiBiKX19CisKKzwhLS0gUHJldHRpZXIgc3RhYmxlIC0tPgorQGxldCBmbiA9IChhKSA9PiAgICAgICAgYT8gICAgMToyOworCit7eyBmbiAoIGEgICAgICAgICBpbnN0YW5jZW9mIGIpfX0KKworPCEtLSBQcmV0dGllciBtYWluIC0tPgorQGxldCBmbiA9IChhKSA9PiAoYSA/IDEgOiAyKTsKKwore3sgZm4oYSBpbnN0YW5jZW9mIGIpIH19CitgYGAKZGlmZiAtLWdpdCBhL3BhY2thZ2UuanNvbiBiL3BhY2thZ2UuanNvbgppbmRleCA3MTcwNzQ3NTYuLjgyODFjMDRkYyAxMDA2NDQKLS0tIGEvcGFja2FnZS5qc29uCisrKyBiL3BhY2thZ2UuanNvbgpAQCAtNDIsNyArNDIsNyBAQAogICAgICJiaW4iCiAgIF0sCiAgICJkZXBlbmRlbmNpZXMiOiB7Ci0gICAgIkBhbmd1bGFyL2NvbXBpbGVyIjogIjIxLjIuNCIsCisgICAgIkBhbmd1bGFyL2NvbXBpbGVyIjogIjIxLjIuOCIsCiAgICAgIkBiYWJlbC9jb2RlLWZyYW1lIjogIjguMC4wLXJjLjMiLAogICAgICJAYmFiZWwvcGFyc2VyIjogIjguMC4wLXJjLjMiLAogICAgICJAYmFiZWwvdHlwZXMiOiAiOC4wLjAtcmMuMyIsCkBAIC01NSw3ICs1NSw3IEBACiAgICAgIkB0eXBlc2NyaXB0LWVzbGludC92aXNpdG9yLWtleXMiOiAiOC41OC4xIiwKICAgICAiYWNvcm4iOiAiOC4xNi4wIiwKICAgICAiYWNvcm4tanN4IjogIjUuMy4yIiwKLSAgICAiYW5ndWxhci1lc3RyZWUtcGFyc2VyIjogIjE1LjMuMCIsCisgICAgImFuZ3VsYXItZXN0cmVlLXBhcnNlciI6ICIxNS40LjMiLAogICAgICJhbmd1bGFyLWh0bWwtcGFyc2VyIjogIjEwLjUuMCIsCiAgICAgImNhbWVsY2FzZSI6ICI5LjAuMCIsCiAgICAgImNpLWluZm8iOiAiNC40LjAiLApkaWZmIC0tZ2l0IGEvc3JjL2xhbmd1YWdlLWpzL3ByaW50L2Z1bmN0aW9uLXBhcmFtZXRlcnMuanMgYi9zcmMvbGFuZ3VhZ2UtanMvcHJpbnQvZnVuY3Rpb24tcGFyYW1ldGVycy5qcwppbmRleCA4ODA5NTQ1NzMuLjRjOTJlNjMyYyAxMDA2NDQKLS0tIGEvc3JjL2xhbmd1YWdlLWpzL3ByaW50L2Z1bmN0aW9uLXBhcmFtZXRlcnMuanMKKysrIGIvc3JjL2xhbmd1YWdlLWpzL3ByaW50L2Z1bmN0aW9uLXBhcmFtZXRlcnMuanMKQEAgLTE4NSw3ICsxODUsMTEgQEAgZnVuY3Rpb24gcHJpbnRGdW5jdGlvblBhcmFtZXRlcnMoCiAgICAgdHlwZVBhcmFtZXRlcnNEb2MsCiAgICAgIigiLAogICAgIGluZGVudChbc29mdGxpbmUsIC4uLnByaW50ZWRdKSwKLSAgICAhaGFzUmVzdFBhcmFtZXRlcihmdW5jdGlvbk5vZGUpID8gcHJpbnRUcmFpbGluZ0NvbW1hKG9wdGlvbnMsICJhbGwiKSA6ICIiLAorICAgICFoYXNSZXN0UGFyYW1ldGVyKGZ1bmN0aW9uTm9kZSkgJiYKKyAgICAvLyBBbmd1bGFyIGRvZXMgbm90IGFsbG93IHRyYWlsaW5nIGNvbW1hCisgICAgcGF0aC5yb290LnR5cGUgIT09ICJOR1Jvb3QiCisgICAgICA/IHByaW50VHJhaWxpbmdDb21tYShvcHRpb25zLCAiYWxsIikKKyAgICAgIDogIiIsCiAgICAgc29mdGxpbmUsCiAgICAgIikiLAogICBdOwpkaWZmIC0tZ2l0IGEveWFybi5sb2NrIGIveWFybi5sb2NrCmluZGV4IDk4YTQxMjRlYi4uZjY0MDE2OTNhIDEwMDY0NAotLS0gYS95YXJuLmxvY2sKKysrIGIveWFybi5sb2NrCkBAIC01LDEyICs1LDEyIEBAIF9fbWV0YWRhdGE6CiAgIHZlcnNpb246IDgKICAgY2FjaGVLZXk6IDEwCiAKLSJAYW5ndWxhci9jb21waWxlckBucG06MjEuMi40IjoKLSAgdmVyc2lvbjogMjEuMi40Ci0gIHJlc29sdXRpb246ICJAYW5ndWxhci9jb21waWxlckBucG06MjEuMi40IgorIkBhbmd1bGFyL2NvbXBpbGVyQG5wbToyMS4yLjgiOgorICB2ZXJzaW9uOiAyMS4yLjgKKyAgcmVzb2x1dGlvbjogIkBhbmd1bGFyL2NvbXBpbGVyQG5wbToyMS4yLjgiCiAgIGRlcGVuZGVuY2llczoKICAgICB0c2xpYjogIm5wbTpeMi4zLjAiCi0gIGNoZWNrc3VtOiAxMC9mMTA3MWRiZmEzOGQ2MTgzZmQ5ZmRjY2ZmMTBhNmQwZTc3YTA4YWZjYjQ3MWQ2MzFlZmZlM2FlNGUyNDJjZWFkZDQ5NjBkZDU4YzhmNTk2NjQ3YmY0YTM5ZTRiODhmYzQ2MTZiZWU1ZDgwYWYzOTNmMjE0MzYzYjQ0NTVhYjI4OAorICBjaGVja3N1bTogMTAvNWJjZjg2NmNmMDFmYmMxOWIwMTZkZjRjNzdhYTY2MjQ0MGJhNWVhM2IxNjE1ZWEyMWI2YzY5Y2Q1MmM1YjZjZjU1ZmJmODAwM2VlOGJjZmQ5MTk5ZGVhYzdkMzNhMzdjY2JmNGI5ZmViNGJjYzIxZTY1Y2FlMzg4ZDJmYTZhODgKICAgbGFuZ3VhZ2VOYW1lOiBub2RlCiAgIGxpbmtUeXBlOiBoYXJkCiAKQEAgLTMwMzksMTIgKzMwMzksMTIgQEAgX19tZXRhZGF0YToKICAgbGFuZ3VhZ2VOYW1lOiBub2RlCiAgIGxpbmtUeXBlOiBoYXJkCiAKLSJhbmd1bGFyLWVzdHJlZS1wYXJzZXJAbnBtOjE1LjMuMCI6Ci0gIHZlcnNpb246IDE1LjMuMAotICByZXNvbHV0aW9uOiAiYW5ndWxhci1lc3RyZWUtcGFyc2VyQG5wbToxNS4zLjAiCisiYW5ndWxhci1lc3RyZWUtcGFyc2VyQG5wbToxNS40LjMiOgorICB2ZXJzaW9uOiAxNS40LjMKKyAgcmVzb2x1dGlvbjogImFuZ3VsYXItZXN0cmVlLXBhcnNlckBucG06MTUuNC4zIgogICBwZWVyRGVwZW5kZW5jaWVzOgotICAgICJAYW5ndWxhci9jb21waWxlciI6ICI+PTIxLjAuNyB8fCA+PSAyMS4xLjAtMCIKLSAgY2hlY2tzdW06IDEwLzkyNjZkNmJmNzNmYWM3ZjJkOTZlNmE3YjMwNmJlZjBhNmI2NTYzZjRmMTFhMzZhYTMwOGQ0Y2EyZmRhMjg2ODgzOWM4ODMxYzJlOTVkNWVhMDA4NjM2ZTA2MWMxY2RjMDY2OGMwMWZjOWY5ODc2ZWIxYjVmNjg0NjIxN2ZjMmVjCisgICAgIkBhbmd1bGFyL2NvbXBpbGVyIjogIj49MjEuMC43IHx8ID49IDIxLjIuMC0wIgorICBjaGVja3N1bTogMTAvYTMxNTY5Yzk5YmI4OWUyMDU2NGMwY2FhNzdjOGRiMjgwNzAxNzBiYmJiMTQ1ZmQ1YWZkZGE3N2EwZDUzNWFmNzFjZDNiZDEzZDFkMjViZDZmZTZjZmZhODFhZTZhYjU3ZjM3Y2QxYTllYTFkY2RiNTAzOWFjNDE5OTQ1NTE1Y2UKICAgbGFuZ3VhZ2VOYW1lOiBub2RlCiAgIGxpbmtUeXBlOiBoYXJkCiAKQEAgLTg1NzksNyArODU3OSw3IEBAIF9fbWV0YWRhdGE6CiAgIHZlcnNpb246IDAuMC4wLXVzZS5sb2NhbAogICByZXNvbHV0aW9uOiAicHJldHRpZXJAd29ya3NwYWNlOi4iCiAgIGRlcGVuZGVuY2llczoKLSAgICAiQGFuZ3VsYXIvY29tcGlsZXIiOiAibnBtOjIxLjIuNCIKKyAgICAiQGFuZ3VsYXIvY29tcGlsZXIiOiAibnBtOjIxLjIuOCIKICAgICAiQGJhYmVsL2NvZGUtZnJhbWUiOiAibnBtOjguMC4wLXJjLjMiCiAgICAgIkBiYWJlbC9nZW5lcmF0b3IiOiAibnBtOjguMC4wLXJjLjMiCiAgICAgIkBiYWJlbC9wYXJzZXIiOiAibnBtOjguMC4wLXJjLjMiCkBAIC04NTk4LDcgKzg1OTgsNyBAQCBfX21ldGFkYXRhOgogICAgICJAdHlwZXNjcmlwdC1lc2xpbnQvdmlzaXRvci1rZXlzIjogIm5wbTo4LjU4LjEiCiAgICAgYWNvcm46ICJucG06OC4xNi4wIgogICAgIGFjb3JuLWpzeDogIm5wbTo1LjMuMiIKLSAgICBhbmd1bGFyLWVzdHJlZS1wYXJzZXI6ICJucG06MTUuMy4wIgorICAgIGFuZ3VsYXItZXN0cmVlLXBhcnNlcjogIm5wbToxNS40LjMiCiAgICAgYW5ndWxhci1odG1sLXBhcnNlcjogIm5wbToxMC41LjAiCiAgICAgYmFzZTY0LWFycmF5YnVmZmVyLWVzNjogIm5wbTozLjEuMCIKICAgICBicm93c2Vyc2xpc3Q6ICJucG06NC4yOC4yIgo=' | base64 -d > /tmp/golden.patch
RUN cp -a /workspace/repo/.yarn /tmp/.yarn_warm 2>/dev/null || true; cd /workspace/repo && ( git apply --3way --ignore-whitespace /tmp/golden.patch || git apply --ignore-whitespace --whitespace=nowarn /tmp/golden.patch || patch -p1 --fuzz=5 -i /tmp/golden.patch ) 2>/dev/null; ( cd /workspace/repo && node .yarn/releases/yarn-4.12.0.cjs install ) 2>&1 | tail -8 || true; cp -an /workspace/repo/.yarn/. /tmp/.yarn_warm/ 2>/dev/null || true; cd /workspace/repo && git checkout -- . 2>/dev/null || true; rm -f '/workspace/repo/changelog_unreleased/angular/18722.md' 2>/dev/null || true; rm -rf /workspace/repo/.yarn && mv /tmp/.yarn_warm /workspace/repo/.yarn 2>/dev/null || true; rm -f /tmp/golden.patch
# offline-enforce: test-time re-install must use the baked cache only
RUN { echo 'enableNetwork: false'; echo 'enableImmutableInstalls: false'; } >> /workspace/repo/.yarnrc.yml 2>/dev/null || true
ENV YARN_ENABLE_NETWORK=false

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) || (dnf install -y tmux asciinema) || (yum install -y tmux asciinema) || true

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# OFFLINE-SIZE: drop submodule git-metadata + logs (after all build/submodule steps)
RUN cd /workspace/repo 2>/dev/null && (git gc --prune=now --aggressive 2>/dev/null || true) && rm -rf .git/modules .git/logs 2>/dev/null || true

# offline login-shell toolchain fix: bake $PATH into /etc/profile.d so `bash -lc` keeps toolchain on PATH
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
