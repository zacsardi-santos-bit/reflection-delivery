FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential gnupg python3 \
    && install -dm 755 /etc/apt/keyrings \
    && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_20.x nodistro main" > /etc/apt/sources.list.d/nodesource.list \
    && apt-get update \
    && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g yarn

RUN mkdir -p /opt/mathjax-renderer-stub/src/css && \
    printf '{"name":"@khanacademy/mathjax-renderer","version":"1.6.2","main":"index.js"}\n' \
        > /opt/mathjax-renderer-stub/package.json && \
    printf 'function MathJaxRenderer(opts) {}\nMathJaxRenderer.prototype.render = function(tex) {\n  var el = (typeof document !== "undefined") ? document.createElement("span") : {};\n  return { domElement: el, addLabelWhenPresentational: function() {} };\n};\nMathJaxRenderer.prototype.updateStyles = function() {};\nmodule.exports = { MathJaxRenderer: MathJaxRenderer };\n' \
        > /opt/mathjax-renderer-stub/index.js && \
    touch /opt/mathjax-renderer-stub/src/css/mathjax.css && \
    touch /opt/mathjax-renderer-stub/src/css/safari-hacks.css && \
    touch /opt/mathjax-renderer-stub/src/css/selectable.css

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/Khan/perseus.git && \
    git fetch --depth 30 origin 6196375add76025fb1f30473912dd38cc001cca4 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN python3 - <<'PYEOF'
import json, re

for pkg_path in ['packages/perseus/package.json', 'packages/math-input/package.json']:
    with open(pkg_path) as f:
        pkg = json.load(f)
    for dep_key in ('dependencies', 'devDependencies', 'peerDependencies'):
        if dep_key in pkg and '@khanacademy/mathjax-renderer' in pkg[dep_key]:
            pkg[dep_key]['@khanacademy/mathjax-renderer'] = 'file:///opt/mathjax-renderer-stub'
    with open(pkg_path, 'w') as f:
        json.dump(pkg, f, indent=2)

with open('yarn.lock') as f:
    content = f.read()

pattern = r'(?m)^"@khanacademy/mathjax-renderer[^"]*":.*?(?=\n\n|\Z)'
content = re.sub(pattern, '', content, flags=re.DOTALL)
content = re.sub(r'\n{3,}', '\n\n', content)

with open('yarn.lock', 'w') as f:
    f.write(content)

print('Done patching package.json files and yarn.lock')
PYEOF

RUN yarn install --no-frozen-lockfile

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
