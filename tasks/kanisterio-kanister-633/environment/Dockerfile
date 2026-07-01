FROM golang:1.21

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/kanisterio/kanister.git && \
    git fetch --depth 30 origin 1bd1f691acd1f9767de46ac28f482576e6d84886 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN go mod download || true

RUN go build ./... || true

RUN mkdir -p /root/.kube && echo "YXBpVmVyc2lvbjogdjEKa2luZDogQ29uZmlnCmNsdXN0ZXJzOgotIGNsdXN0ZXI6CiAgICBzZXJ2ZXI6IGh0dHA6Ly9sb2NhbGhvc3Q6MTgwODAKICBuYW1lOiBmYWtlLWNsdXN0ZXIKY29udGV4dHM6Ci0gY29udGV4dDoKICAgIGNsdXN0ZXI6IGZha2UtY2x1c3RlcgogICAgdXNlcjogZmFrZS11c2VyCiAgbmFtZTogZmFrZS1jb250ZXh0CmN1cnJlbnQtY29udGV4dDogZmFrZS1jb250ZXh0CnVzZXJzOgotIG5hbWU6IGZha2UtdXNlcgogIHVzZXI6IHt9Cg==" | base64 -d > /root/.kube/config

RUN echo "cGFja2FnZSBtYWluCgppbXBvcnQgKAoJImZtdCIKCSJsb2ciCgkibmV0L2h0dHAiCgkic3RyaW5ncyIKKQoKZnVuYyBtYWluKCkgewoJaHR0cC5IYW5kbGVGdW5jKCIvIiwgZnVuYyh3IGh0dHAuUmVzcG9uc2VXcml0ZXIsIHIgKmh0dHAuUmVxdWVzdCkgewoJCXcuSGVhZGVyKCkuU2V0KCJDb250ZW50LVR5cGUiLCAiYXBwbGljYXRpb24vanNvbiIpCgkJcCA6PSByLlVSTC5QYXRoCgkJbSA6PSByLk1ldGhvZAoKCQlpZiBwID09ICIvYXBpIiB7CgkJCWZtdC5GcHJpbnQodywgYHsia2luZCI6IkFQSVZlcnNpb25zIiwiYXBpVmVyc2lvbiI6InYxIiwidmVyc2lvbnMiOlsidjEiXSwic2VydmVyQWRkcmVzc0J5Q2xpZW50Q0lEUnMiOlt7ImNsaWVudENJRFIiOiIwLjAuMC4wLzAiLCJzZXJ2ZXJBZGRyZXNzIjoibG9jYWxob3N0OjE4MDgwIn1dfWApCgkJCXJldHVybgoJCX0KCQlpZiBwID09ICIvYXBpcyIgewoJCQlmbXQuRnByaW50KHcsIGB7ImtpbmQiOiJBUElHcm91cExpc3QiLCJhcGlWZXJzaW9uIjoidjEiLCJncm91cHMiOlt7Im5hbWUiOiJzdG9yYWdlLms4cy5pbyIsInZlcnNpb25zIjpbeyJncm91cFZlcnNpb24iOiJzdG9yYWdlLms4cy5pby92MSIsInZlcnNpb24iOiJ2MSJ9XSwicHJlZmVycmVkVmVyc2lvbiI6eyJncm91cFZlcnNpb24iOiJzdG9yYWdlLms4cy5pby92MSIsInZlcnNpb24iOiJ2MSJ9fSx7Im5hbWUiOiJzbmFwc2hvdC5zdG9yYWdlLms4cy5pbyIsInZlcnNpb25zIjpbeyJncm91cFZlcnNpb24iOiJzbmFwc2hvdC5zdG9yYWdlLms4cy5pby92MWFscGhhMSIsInZlcnNpb24iOiJ2MWFscGhhMSJ9XSwicHJlZmVycmVkVmVyc2lvbiI6eyJncm91cFZlcnNpb24iOiJzbmFwc2hvdC5zdG9yYWdlLms4cy5pby92MWFscGhhMSIsInZlcnNpb24iOiJ2MWFscGhhMSJ9fV19YCkKCQkJcmV0dXJuCgkJfQoJCWlmIHN0cmluZ3MuSGFzUHJlZml4KHAsICIvYXBpcy9zdG9yYWdlLms4cy5pby92MSIpICYmIG0gPT0gIkdFVCIgewoJCQlmbXQuRnByaW50KHcsIGB7ImtpbmQiOiJTdG9yYWdlQ2xhc3NMaXN0IiwiYXBpVmVyc2lvbiI6InN0b3JhZ2UuazhzLmlvL3YxIiwibWV0YWRhdGEiOnsicmVzb3VyY2VWZXJzaW9uIjoiMSJ9LCJpdGVtcyI6W119YCkKCQkJcmV0dXJuCgkJfQoJCWlmIHN0cmluZ3MuSGFzUHJlZml4KHAsICIvYXBpL3YxIikgewoJCQlpZiBzdHJpbmdzLkNvbnRhaW5zKHAsICJuYW1lc3BhY2VzIikgJiYgbSA9PSAiUE9TVCIgewoJCQkJdy5Xcml0ZUhlYWRlcigyMDEpCgkJCQlmbXQuRnByaW50KHcsIGB7ImtpbmQiOiJOYW1lc3BhY2UiLCJhcGlWZXJzaW9uIjoidjEiLCJtZXRhZGF0YSI6eyJuYW1lIjoidGVzdC1ucyIsInJlc291cmNlVmVyc2lvbiI6IjEiLCJ1aWQiOiJhYmMxMjMifX1gKQoJCQkJcmV0dXJuCgkJCX0KCQkJaWYgc3RyaW5ncy5Db250YWlucyhwLCAibmFtZXNwYWNlcyIpICYmIG0gPT0gIkdFVCIgJiYgc3RyaW5ncy5IYXNTdWZmaXgocCwgIm5hbWVzcGFjZXMiKSB7CgkJCQlmbXQuRnByaW50KHcsIGB7ImtpbmQiOiJOYW1lc3BhY2VMaXN0IiwiYXBpVmVyc2lvbiI6InYxIiwibWV0YWRhdGEiOnsicmVzb3VyY2VWZXJzaW9uIjoiMSJ9LCJpdGVtcyI6W119YCkKCQkJCXJldHVybgoJCQl9CgkJCWlmIHN0cmluZ3MuQ29udGFpbnMocCwgIm5hbWVzcGFjZXMiKSAmJiBtID09ICJHRVQiIHsKCQkJCWZtdC5GcHJpbnQodywgYHsia2luZCI6Ik5hbWVzcGFjZSIsImFwaVZlcnNpb24iOiJ2MSIsIm1ldGFkYXRhIjp7Im5hbWUiOiJkZWZhdWx0IiwicmVzb3VyY2VWZXJzaW9uIjoiMSJ9fWApCgkJCQlyZXR1cm4KCQkJfQoJCQlpZiBtID09ICJERUxFVEUiIHsKCQkJCWZtdC5GcHJpbnQodywgYHsia2luZCI6IlN0YXR1cyIsImFwaVZlcnNpb24iOiJ2MSIsInN0YXR1cyI6IlN1Y2Nlc3MiLCJjb2RlIjoyMDB9YCkKCQkJCXJldHVybgoJCQl9CgkJCWlmIG0gPT0gIkdFVCIgewoJCQkJZm10LkZwcmludCh3LCBgeyJraW5kIjoiTGlzdCIsImFwaVZlcnNpb24iOiJ2MSIsIm1ldGFkYXRhIjp7InJlc291cmNlVmVyc2lvbiI6IjEifSwiaXRlbXMiOltdfWApCgkJCQlyZXR1cm4KCQkJfQoJCX0KCQlpZiBzdHJpbmdzLkhhc1ByZWZpeChwLCAiL2FwaXMvc25hcHNob3Quc3RvcmFnZS5rOHMuaW8vIikgJiYgbSA9PSAiR0VUIiB7CgkJCWlmIHN0cmluZ3MuSGFzU3VmZml4KHAsICJ2b2x1bWVzbmFwc2hvdGNsYXNzZXMiKSB7CgkJCQlncm91cCA6PSAic25hcHNob3Quc3RvcmFnZS5rOHMuaW8vdjFhbHBoYTEiCgkJCQlpZiBzdHJpbmdzLkNvbnRhaW5zKHAsICJ2MWJldGExIikgewoJCQkJCWdyb3VwID0gInNuYXBzaG90LnN0b3JhZ2UuazhzLmlvL3YxYmV0YTEiCgkJCQl9CgkJCQlmbXQuRnByaW50Zih3LCBgeyJraW5kIjoiVm9sdW1lU25hcHNob3RDbGFzc0xpc3QiLCJhcGlWZXJzaW9uIjoiJXMiLCJtZXRhZGF0YSI6eyJyZXNvdXJjZVZlcnNpb24iOiIxIn0sIml0ZW1zIjpbXX1gLCBncm91cCkKCQkJCXJldHVybgoJCQl9CgkJCWlmIHN0cmluZ3MuSGFzU3VmZml4KHAsICJ2b2x1bWVzbmFwc2hvdHMiKSB7CgkJCQlmbXQuRnByaW50KHcsIGB7ImtpbmQiOiJWb2x1bWVTbmFwc2hvdExpc3QiLCJhcGlWZXJzaW9uIjoic25hcHNob3Quc3RvcmFnZS5rOHMuaW8vdjFhbHBoYTEiLCJtZXRhZGF0YSI6eyJyZXNvdXJjZVZlcnNpb24iOiIxIn0sIml0ZW1zIjpbXX1gKQoJCQkJcmV0dXJuCgkJCX0KCQkJaWYgc3RyaW5ncy5IYXNTdWZmaXgocCwgInZvbHVtZXNuYXBzaG90Y29udGVudHMiKSB7CgkJCQlmbXQuRnByaW50KHcsIGB7ImtpbmQiOiJWb2x1bWVTbmFwc2hvdENvbnRlbnRMaXN0IiwiYXBpVmVyc2lvbiI6InNuYXBzaG90LnN0b3JhZ2UuazhzLmlvL3YxYWxwaGExIiwibWV0YWRhdGEiOnsicmVzb3VyY2VWZXJzaW9uIjoiMSJ9LCJpdGVtcyI6W119YCkKCQkJCXJldHVybgoJCQl9CgkJCXcuV3JpdGVIZWFkZXIoNDA0KQoJCQlmbXQuRnByaW50KHcsIGB7ImtpbmQiOiJTdGF0dXMiLCJhcGlWZXJzaW9uIjoidjEiLCJzdGF0dXMiOiJGYWlsdXJlIiwicmVhc29uIjoiTm90Rm91bmQiLCJjb2RlIjo0MDR9YCkKCQkJcmV0dXJuCgkJfQoJCWlmIG0gPT0gIkRFTEVURSIgewoJCQlmbXQuRnByaW50KHcsIGB7ImtpbmQiOiJTdGF0dXMiLCJhcGlWZXJzaW9uIjoidjEiLCJzdGF0dXMiOiJTdWNjZXNzIiwiY29kZSI6MjAwfWApCgkJCXJldHVybgoJCX0KCQlsb2cuUHJpbnRmKCJ1bmhhbmRsZWQ6ICVzICVzIiwgbSwgcCkKCQl3LldyaXRlSGVhZGVyKDQwNCkKCQlmbXQuRnByaW50KHcsIGB7ImtpbmQiOiJTdGF0dXMiLCJhcGlWZXJzaW9uIjoidjEiLCJzdGF0dXMiOiJGYWlsdXJlIiwicmVhc29uIjoiTm90Rm91bmQiLCJjb2RlIjo0MDR9YCkKCX0pCglsb2cuUHJpbnRsbigiTW9jayBrOHMgQVBJIGxpc3RlbmluZyBvbiA6MTgwODAiKQoJbG9nLkZhdGFsKGh0dHAuTGlzdGVuQW5kU2VydmUoIjoxODA4MCIsIG5pbCkpCn0K" | base64 -d > /tmp/mockk8s.go && cd /tmp && go build -o /usr/local/bin/mockk8s mockk8s.go

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
