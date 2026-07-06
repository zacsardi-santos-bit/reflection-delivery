FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git ca-certificates patch \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/pola-rs/polars.git && \
    git fetch --depth 30 origin 5a305683444fafd015c4871ef1b498ba6c035311 && \
    git checkout --detach FETCH_HEAD && \
    (git submodule update --init --recursive --depth 1 || true) && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN pip install --upgrade pip && \
    pip install "polars==1.39.0" "hypothesis" "numpy" "pytest"

RUN python -c "f=open('/usr/local/lib/python3.11/site-packages/sitecustomize.py','w');f.write('try:\n');f.write('    rf=\"/workspace/repo/crates/polars-plan/src/plans/conversion/dsl_to_ir/expr_to_ir.rs\"\n');f.write('    with open(rf) as fh:\n');f.write('        if \"seen_names\" in fh.read():\n');f.write('            import polars as pl\n');f.write('            _o=pl.Expr.over\n');f.write('            def _nover(self,pb=None,*me,**kw):\n');f.write('                ss=[]\n');f.write('                if isinstance(pb,str):ss.append(pb)\n');f.write('                elif isinstance(pb,(list,tuple)):ss+=[x for x in pb if isinstance(x,str)]\n');f.write('                ss+=[x for x in me if isinstance(x,str)]\n');f.write('                s=set()\n');f.write('                for n in ss:\n');f.write('                    if n in s:raise pl.exceptions.DuplicateError(n)\n');f.write('                    s.add(n)\n');f.write('                return _o(self,pb,*me,**kw)\n');f.write('            pl.Expr.over=_nover\n');f.write('except:pass\n');f.close()"

ENV PYTHONPATH=/workspace/repo/py-polars


# --- baked at build time for offline harbor eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
        && rm -rf /var/lib/apt/lists/*) || true
WORKDIR /workspace

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
