# Plugin Spec Compliance Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Convert langfuse-observability plugin from JSON manifests to YAML, remove committed credentials, and add community-ready files (README, LICENSE) per AGENTS.plugins.md spec.

**Architecture:** In-place file migration. No code changes. Convert manifest/config formats, add community files, clean up credentials.

**Tech Stack:** YAML, Git

---

### Task 1: Create .gitignore

**Files:**
- Create: `.gitignore`

**Step 1: Create .gitignore file**

```
config.json
__pycache__/
*.pyc
```

Write this exact content to `.gitignore` at the repo root.

**Step 2: Verify .gitignore works**

Run: `git status`
Expected: `.gitignore` shows as untracked. `config.json` should no longer appear as untracked (it was already tracked, so it will still show until removed).

**Step 3: Commit**

```bash
git add .gitignore
git commit -m "chore: add .gitignore for credentials and bytecode"
```

---

### Task 2: Convert plugin.json to plugin.yaml

**Files:**
- Create: `plugin.yaml`
- Delete: `plugin.json`

**Step 1: Create plugin.yaml**

Write this exact content to `plugin.yaml` at the repo root:

```yaml
title: Observability
description: Langfuse tracing, trace viewer, chat forking, prompt lab, and split-screen comparison.
version: 1.0.0
settings_sections:
  - agent
per_project_config: false
per_agent_config: false
always_enabled: false
```

Note: The JSON had `"name"` — the YAML spec uses `"title"` instead. The `always_enabled` field is new (defaults to false per spec).

**Step 2: Delete plugin.json**

Run: `git rm plugin.json`

**Step 3: Verify plugin.yaml content**

Run: `cat plugin.yaml`
Expected: The exact YAML content above, 8 lines, no JSON syntax.

**Step 4: Commit**

```bash
git add plugin.yaml
git commit -m "chore: convert plugin.json to plugin.yaml per Agent Zero spec"
```

---

### Task 3: Convert config.default.json to default_config.yaml

**Files:**
- Create: `default_config.yaml`
- Delete: `config.default.json`

**Step 1: Create default_config.yaml**

Write this exact content to `default_config.yaml` at the repo root:

```yaml
langfuse_enabled: false
langfuse_public_key: ""
langfuse_secret_key: ""
langfuse_host: "https://cloud.langfuse.com"
langfuse_sample_rate: 1.0
```

**Step 2: Delete config.default.json**

Run: `git rm config.default.json`

**Step 3: Verify default_config.yaml content**

Run: `cat default_config.yaml`
Expected: The exact YAML content above, 5 lines.

**Step 4: Commit**

```bash
git add default_config.yaml
git commit -m "chore: convert config.default.json to default_config.yaml per Agent Zero spec"
```

---

### Task 4: Remove committed credentials

**Files:**
- Delete: `config.json`

**Step 1: Remove config.json from git tracking**

This file contains real Langfuse API keys. Remove it from the repo.

Run: `git rm config.json`

**Step 2: Verify it is ignored**

Run: `git status`
Expected: `config.json` does not appear (it's in `.gitignore` from Task 1).

**Step 3: Commit**

```bash
git commit -m "security: remove committed Langfuse credentials

config.json contained real API keys. Credentials should be entered
via the settings UI or LANGFUSE_* environment variables."
```

---

### Task 5: Add LICENSE

**Files:**
- Create: `LICENSE`

**Step 1: Create MIT License file**

Write the MIT License to `LICENSE` at the repo root. Use the current year (2026) and copyright holder name from git config, or "langfuse-observability contributors" as fallback.

```
MIT License

Copyright (c) 2026 langfuse-observability contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

**Step 2: Commit**

```bash
git add LICENSE
git commit -m "chore: add MIT license"
```

---

### Task 6: Add README.md

**Files:**
- Create: `README.md`

**Step 1: Create README.md**

Write a concise README to `README.md` at the repo root:

```markdown
# Langfuse Observability

Agent Zero plugin for LLM observability via [Langfuse](https://langfuse.com).

## Features

- **Tracing** — Automatic tracing of LLM calls, tool executions, and agent interactions
- **Trace Viewer** — Browse and inspect traces from the Agent Zero sidebar
- **Chat Forking** — Fork any conversation at any point to explore alternatives
- **Prompt Lab** — Test and refine prompts with judge-based evaluation
- **Split View** — Side-by-side comparison of chat responses

## Installation

Clone or download this repo into your Agent Zero plugins directory:

```bash
git clone <repo-url> /path/to/agent-zero/usr/plugins/langfuse-observability
```

## Configuration

Configure via the Agent Zero settings UI (Agent tab > Observability section), or set environment variables:

- `LANGFUSE_PUBLIC_KEY` — Your Langfuse project public key
- `LANGFUSE_SECRET_KEY` — Your Langfuse project secret key
- `LANGFUSE_HOST` — Langfuse instance URL (default: `https://cloud.langfuse.com`)

## Requirements

The `langfuse` Python package is auto-installed on first use.
```

**Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add README for Plugin List UI and community users"
```

---

### Task 7: Final verification

**Step 1: Verify directory structure matches spec**

Run: `find . -maxdepth 1 -not -path './.git' -not -path '.' | sort`

Expected output should include:
```
./.gitignore
./LICENSE
./README.md
./AGENTS.plugins.md
./__init__.py
./api
./default_config.yaml
./docs
./extensions
./helpers
./plugin.yaml
./prompts
./requirements.txt
./webui
```

Should NOT include: `plugin.json`, `config.default.json`, `config.json`.

**Step 2: Verify no JSON manifests remain**

Run: `ls plugin.json config.default.json config.json 2>&1`
Expected: All three report "No such file or directory".

**Step 3: Verify git is clean**

Run: `git status`
Expected: Nothing to commit, working tree clean.
