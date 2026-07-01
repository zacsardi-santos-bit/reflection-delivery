FROM node:20-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential python3 \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g pnpm@8.10.5

RUN curl -L https://github.com/foundry-rs/foundry/releases/download/nightly/foundry_nightly_linux_amd64.tar.gz | tar -xzC /usr/local/bin

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/wevm/wagmi.git && \
    git fetch --depth 30 origin 668b5e7128ec757fd3311bd41fb4c1177780efeb && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN node -e "const fs=require('fs'); const p=JSON.parse(fs.readFileSync('package.json','utf8')); p.scripts.postinstall='echo skip'; fs.writeFileSync('package.json', JSON.stringify(p, null, 2))"

RUN pnpm install --frozen-lockfile

RUN node -e " \
const fs=require('fs'), path=require('path'); \
function walk(d,r=[]){ \
  const b=path.basename(d); \
  if(['node_modules','dist','.git'].includes(b))return r; \
  let e;try{e=fs.readdirSync(d)}catch{return r} \
  for(const x of e){ \
    const p2=path.join(d,x); \
    try{const s=fs.statSync(p2);if(s.isDirectory())walk(p2,r);else if(x==='package.json')r.push(p2)}catch{} \
  } \
  return r; \
} \
const R='/workspace/repo'; \
console.log('Creating dist symlinks...'); \
for(const pp of walk(R)){ \
  let pj;try{pj=JSON.parse(fs.readFileSync(pp,'utf8'))}catch{continue} \
  const{name,private:priv,exports:ex,bin}=pj; \
  if(priv&&name!=='@wagmi/test')continue; \
  if(!ex||bin)continue; \
  const d=path.dirname(pp),dist=path.join(d,'dist'); \
  try{fs.rmSync(dist,{recursive:true,force:true})}catch{} \
  fs.mkdirSync(dist,{recursive:true}); \
  for(const[k,e] of Object.entries(ex)){ \
    if(/package\\.json\$/.test(k)||typeof e==='string')continue; \
    for(const[t,v] of Object.entries(e)){ \
      const sd=path.dirname(v).replace(t==='default'?'dist/esm':'dist/'+t,'src'); \
      const sf=k==='.'?'index.ts':path.basename(k)+'.ts'; \
      const sp=path.resolve(d,path.join(sd,sf)); \
      const dd=path.resolve(d,path.dirname(v)); \
      const dp=path.join(dd,path.basename(v)); \
      fs.mkdirSync(dd,{recursive:true}); \
      try{fs.unlinkSync(dp)}catch{} \
      fs.symlinkSync(sp,dp); \
    } \
  } \
  console.log(name); \
} \
console.log('Done.'); \
"

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
