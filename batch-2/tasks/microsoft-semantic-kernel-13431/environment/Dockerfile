FROM mcr.microsoft.com/dotnet/sdk:10.0

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch python3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/microsoft/semantic-kernel.git && \
    git fetch --depth 30 --no-tags origin 52d4e5ce857bcdc770eb9ea415e2092aae3fa258 && \
    git checkout FETCH_HEAD && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf /workspace/repo/.git/logs
WORKDIR /workspace/repo/dotnet

RUN dotnet restore src/SemanticKernel.UnitTests/SemanticKernel.UnitTests.csproj && \
    dotnet restore src/Connectors/Connectors.OpenAI.UnitTests/Connectors.OpenAI.UnitTests.csproj

RUN printf "import sys, xml.etree.ElementTree as ET\nNS = 'http://microsoft.com/schemas/VisualStudio/TeamTest/2010'\nroot = ET.Element('testsuites')\nsuite = ET.SubElement(root, 'testsuite', name='dotnet')\nfor f in sys.argv[1:]:\n    try:\n        tree = ET.parse(f)\n        for r in tree.findall('.//{%%s}UnitTestResult' %% NS):\n            n = r.get('testName', '')\n            parts = n.rsplit('.', 1)\n            classname = parts[0] if len(parts) > 1 else ''\n            method = parts[-1]\n            tc = ET.SubElement(suite, 'testcase', classname=classname, name=method)\n            if r.get('outcome') in ('Failed', 'Error'):\n                me = r.find('.//{%%s}Message' %% NS)\n                ET.SubElement(tc, 'failure').text = me.text if me is not None else ''\n    except Exception:\n        pass\nprint(ET.tostring(root, encoding='unicode'))\n" > /tmp/trx2junit.py


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
