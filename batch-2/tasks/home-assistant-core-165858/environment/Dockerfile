FROM python:3.14-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    libssl-dev libffi-dev zlib1g-dev libjpeg-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/home-assistant/core.git && \
    git fetch --depth 30 --no-tags origin 488d9ad75cc9f941689e6d8e14e0c50b92d88984 && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git reflog expire --expire=now --all && \
    rm -rf /workspace/repo/.git/logs && \
    git gc --prune=now
WORKDIR /workspace/repo

RUN pip install --no-cache-dir -e "."

RUN pip install --no-cache-dir aiohasupervisor==0.4.2

RUN pip install --no-cache-dir \
    pytest==9.0.0 \
    pytest-asyncio==1.3.0 \
    pytest-freezer==0.4.9 \
    pytest-socket==0.7.0 \
    syrupy==5.0.0 \
    freezegun==1.5.2 \
    requests-mock==1.12.1 \
    respx==0.22.0

RUN pip install --no-cache-dir growattServer==1.9.0

RUN python3 << 'PYEOF'
import json, re, os

def load_json(path):
    with open(path) as f:
        return json.load(f)

def flatten_dict(data, prefix=''):
    result = {}
    for key, value in data.items():
        full_key = f"{prefix}::{key}" if prefix else key
        if isinstance(value, dict):
            result.update(flatten_dict(value, full_key))
        else:
            result[full_key] = value
    return result

ha_strings = load_json('/workspace/repo/homeassistant/strings.json')
gwt_strings = load_json('/workspace/repo/homeassistant/components/growatt_server/strings.json')

lookup = {}
lookup.update(flatten_dict(ha_strings.get('common', {}), 'common'))
lookup.update(flatten_dict(gwt_strings, 'component::growatt_server'))

def resolve_value(val, depth=0):
    if depth > 10:
        return val
    if isinstance(val, str):
        match = re.fullmatch(r'\[%key:(.+?)%\]', val)
        if match:
            key = match.group(1)
            resolved = lookup.get(key, val)
            if resolved != val:
                return resolve_value(resolved, depth + 1)
        return val
    elif isinstance(val, dict):
        return {k: resolve_value(v, depth) for k, v in val.items()}
    elif isinstance(val, list):
        return [resolve_value(v, depth) for v in val]
    return val

resolved = resolve_value(gwt_strings)

dst = '/workspace/repo/homeassistant/components/growatt_server/translations'
os.makedirs(dst, exist_ok=True)
with open(f'{dst}/en.json', 'w') as f:
    json.dump(resolved, f, indent=2)
print('Translation file generated successfully')
PYEOF


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


# offline login-shell PATH bake
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh

# offline-harbor: git blob-drop shrink (client-contract .git<100MB, HEAD==base)
RUN set +e; cd /workspace/repo 2>/dev/null && ( rm -rf .git/modules .git/worktrees 2>/dev/null; git reflog expire --expire=now --all 2>/dev/null; rm -rf .git/logs; git remote remove origin 2>/dev/null; git tag -l | xargs -r git tag -d >/dev/null 2>&1; git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d 2>/dev/null; git cat-file --batch-all-objects --batch-check='%(objectname) %(objecttype)' 2>/dev/null | awk '$2!="blob"{print $1}' > /tmp/_keep.txt; if [ -s /tmp/_keep.txt ]; then git pack-objects --quiet /tmp/_kp < /tmp/_keep.txt >/dev/null 2>&1 && rm -rf .git/objects/pack && mkdir -p .git/objects/pack && find .git/objects -maxdepth 1 -type d -name '[0-9a-f][0-9a-f]' -exec rm -rf {} + 2>/dev/null; mv /tmp/_kp-*.pack /tmp/_kp-*.idx .git/objects/pack/ 2>/dev/null; fi; git rev-parse HEAD >/dev/null 2>&1 || echo "WARN head-broken"; du -sm .git 2>/dev/null | cut -f1 ) ; true
