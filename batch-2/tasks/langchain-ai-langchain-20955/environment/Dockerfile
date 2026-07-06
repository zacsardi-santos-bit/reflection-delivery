FROM python:3.11-slim

RUN apt-get update && apt-get install -y \
    git curl ca-certificates patch build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/langchain-ai/langchain.git && \
    git fetch --depth 30 origin 8bbdb4f6a04fb7b9cb109ae3a621b7d8ec98b7b1 && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

WORKDIR /workspace/repo

RUN pip install --upgrade pip setuptools wheel 2>&1 | tail -5

RUN pip install -e libs/core/ 2>&1 | tail -5
RUN pip install -e libs/text-splitters/ 2>&1 | tail -5
RUN pip install -e libs/community/ 2>&1 | tail -5

RUN pip install pytest pytest-asyncio syrupy requests 2>&1 | tail -5

RUN cat > /usr/local/lib/sambanova_conftest.py << 'CONFEOF'
import pytest
from unittest.mock import MagicMock


class MockSambaverseResponse:
    status_code = 200
    text = 'data: {"completion": "LangChain is a framework for building LLM-powered applications."}'

    def json(self):
        return {"data": [{"completion": "LangChain is a framework for building LLM-powered applications."}]}


@pytest.fixture(autouse=True)
def mock_sambanova_all(monkeypatch):
    monkeypatch.setenv("SAMBAVERSE_API_KEY", "fake_api_key")
    monkeypatch.setenv("SAMBASTUDIO_BASE_URL", "http://fake.sambastudio.example.com")
    monkeypatch.setenv("SAMBASTUDIO_PROJECT_ID", "fake_project_id")
    monkeypatch.setenv("SAMBASTUDIO_ENDPOINT_ID", "fake_endpoint_id")
    monkeypatch.setenv("SAMBASTUDIO_API_KEY", "fake_api_key")

    mock_session = MagicMock()
    mock_session.post.return_value = MockSambaverseResponse()
    monkeypatch.setattr("requests.Session", lambda: mock_session)
CONFEOF

WORKDIR /workspace

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
