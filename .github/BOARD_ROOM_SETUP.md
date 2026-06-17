# 🎯 Board Room Setup Guide

> Kanban board + workflow automation for the Hermes Multi-Agent Workflow

---

## Step 1: Create the Board Room Project

Since fine-grained PATs cannot create GitHub Projects via API, you must create the board manually:

1. Go to **https://github.com/users/liferoundhere/projects**
2. Click **"New project"**
3. Select **"Board"** (Kanban template)
4. Name it: **"Board Room"**
5. Click **"Create"**

## Step 2: Configure Board Columns

Add these columns to match the multi-agent workflow pipeline:

| Column | Purpose |
|--------|---------|
| **📝 To Do** | New tasks, scout findings, proposals awaiting triage |
| **🔄 Ready** | Scored ≥65/100, approved for research/build |
| **🚀 In Progress** | Active agent work (research, build, test, video) |
| **✅ Done** | Completed and verified deliverables |
| **💾 Shelved** | Archived proposals (score <65 or human /shelve) |

## Step 3: Enable Built-in Automations

In the project settings, enable:

- [ ] **Auto-add to project** → Select `hermes-multi-agent-workflow` repo
- [ ] **Auto-archive** → Move `Done` items after 30 days
- [ ] **Status sync** → Link PR merge → `Done`, Issue close → `Done`

## Step 4: Update Workflow Files

After creating the project, find your **project number** in the URL:
```
https://github.com/users/liferoundhere/projects/3  ← number is 3
```

Edit these workflow files and update `PROJECT_NUMBER`:
- `.github/workflows/multi-agent-board.yml`
- `.github/workflows/board-room-automation.yml`

## Step 5: How the Workflow Works

### Issue/PR Lifecycle

```
New Issue/PR opened
    ↓
Auto-added to Board Room (To Do column)
    ↓
Auto-labeled by agent type (scout, orchestrator, builder, etc.)
    ↓
Human moves to Ready / In Progress / Done via board
    ↓
Status changes sync back to labels and comments
```

### Human Gate Commands

When a proposal reaches the approval gate, comment on the issue:

| Command | Action |
|---------|--------|
| `/approve` | Triggers fulfillment. Labels: `approved`, `status: in-progress` |
| `/shelve` | Archives task. Labels: `shelved`. Closes issue. |
| `/modify` | Sends back for revision. Labels: `needs-modification` |

### Agent Path Labels

| Label | Meaning |
|-------|---------|
| `path: build` | Route to CLI tool/skill builder |
| `path: video` | Route to slide deck + script producer |
| `scout` | Discovery task from X/Web scout |
| `orchestrator` | Scoring/routing task |
| `research` | Verification task |
| `builder` | Implementation task |
| `testing` | QA validation task |

---

## Workflow Files

| File | Purpose |
|------|---------|
| `multi-agent-board.yml` | Main automation: add-to-board, auto-label, human gate |
| `board-room-automation.yml` | Status sync, draft/ready triggers |
| `project-setup.yml` | Diagnostic workflow for project configuration |

---

## Troubleshooting

**Workflow fails with "Resource not accessible"**
- Ensure `GITHUB_TOKEN` in repo Settings → Secrets → Actions has `repo` and `project` scopes
- For fine-grained tokens, grant "Projects" read/write permission

**Items not appearing on board**
- Verify `PROJECT_NUMBER` matches your actual project URL
- Check project "Auto-add" settings include this repository

**Human gate commands not working**
- Ensure the issue has the `proposal` or `ready` label
- The workflow only triggers on `/approve`, `/shelve`, `/modify` in comments
