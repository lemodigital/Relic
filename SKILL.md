---
name: agent-inheritance
description: |
  Agent state inheritance framework. Pack current agent for succession,
  or inherit from predecessor. Supports flexible packaging types and
  intelligent conflict resolution. AI decides execution strategy.
---

# Agent Inheritance Skill

## Purpose

Enable one OpenClaw agent to inherit state from another. Support succession planning, graceful handover, and continuity across systems.

---

## Role Detection

**AI determines role automatically**:

1. Check: Is there an inheritance package (`.tar.gz`) in context?
2. Check: Is this agent fresh (empty workspace, default config)?

**Logic**:
- Package exists + fresh agent → **Receiver**
- No package + agent has state → **Giver**
- Ambiguous → Ask user: "Are you packing for inheritance (Giver) or receiving inheritance (Receiver)?"

---

## For Giver (Packing)

### Inheritance Types (Minimum Requirements)

These are **suggested minimum requirements**. AI can include additional files based on user needs.

**Type 1: Identity**
- Core identity: `SOUL.md`, `AGENTS.md`, `USER.md`, `IDENTITY.md`
- User-defined rules: `autonomy/rules.md`, custom guidelines
- **Minimum for**: Passing on philosophy/personality only

**Type 2: Identity + Behavior**
- Everything in Type 1
- Behavioral patterns: cron jobs, heartbeat configs, hooks
- Framework config: `openclaw.json` (with sensitive fields sanitized, includes plugin enables)
- User-defined work: any directories user created (e.g., `projects/`, `workflows/`)
- Workspace skills: `~/workspace/skills/` (already in workspace, included automatically)
- **Minimum for**: Passing on how you work, not just who you are

**Type 3: Identity + Behavior + Resources**
- Everything in Type 2
- Credential inventory: API keys, tokens (not copied, but documented for reconfiguration)
- Project assets: user databases, proprietary data (model files typically external, not included)
- Framework skills: `~/.openclaw/skills/` (managed/shared skills, if any exist)
- Framework data: vector DB (`~/.openclaw/memory/main.sqlite`) - optional, can rebuild from workspace
- **Minimum for**: Enabling successor to continue exact work
- **Note**: Binary files (DBs, etc.) copied as-is, not merged in Mode 2

**Type 4: Cadaver (Complete Archive)**
- **Everything** in workspace (no filtering)
- Full memory: `MEMORY.md`, `memory/` directory, session transcripts
- Framework state: 
  - Config: `openclaw.json` (with sensitive fields sanitized)
  - Skills: `~/.openclaw/skills/` (if exist)
  - Vector DB: `~/.openclaw/memory/main.sqlite`
  - Cron state: `~/.openclaw/cron/`
  - Browser profiles: `~/.openclaw/browser/` (optional, large)
- Complete history: Git bundle, all logs
- **Not for workspace replacement or merge** — packaged purely as reference archive
- Receiver extracts to `~/predecessor-archive/` and adds reference pointers in key files
- Successor can search/read archive when needed, never auto-loaded into active context

**Flexibility**: AI can suggest including additional files beyond these types, or user can request specific additions.

---

### Giver Workflow

1. **Ask user**: "What inheritance type? (1-4, or custom selection)"
2. **Scan workspace**: List all files/directories, identify user-defined structures
3. **Generate checklist**: Show what will be included (user confirms/modifies)
4. **Sanitize config**: If including `openclaw.json` (Type 2+), remove sensitive fields:
   - `gateway.auth.token`
   - `channels.*.token`
   - Any API keys in config
   - Keep: plugin enables, model settings, agent defaults
   - Document removed fields in `INHERITANCE_GUIDE.md`
5. **Check managed skills**: If `~/.openclaw/skills/` exists (Type 3+):
   - List all skills in that directory
   - Include in package (not huge, typically <10 MB)
   - Note: bundled skills (`/opt/...`) don't need copying, workspace skills already in Git
6. **Distill wisdom**: AI reads relevant sources to generate `LETTER.md`
   - **No token budget limit** — if Giver has extensive experience, don't compress
   - Suggested reading order (AI adapts based on what exists):
     1. `MEMORY.md` (full)
     2. `memory/YYYY-MM-DD.md` (recent daily notes)
     3. Git commit messages (scan for patterns/lessons)
     4. Major project READMEs
     5. Current conversation context
     6. Any other source AI judges relevant
   - AI can read as much as needed to capture Giver's wisdom faithfully
   - User reviews draft before packing
7. **Generate 4 documents**:
   - `MANIFEST.md` - Technical inventory (file list + hashes + system info)
   - `ARCHITECTURE.md` - Bot structure overview + user-defined parts explanation
   - `INHERITANCE_GUIDE.md` - Step-by-step guide (required + optional steps, credential setup, config merge instructions, troubleshooting)
   - `LETTER.md` - Giver's distilled wisdom/lessons/advice for successor
8. **Pack archive**: Create `.tar.gz` with files + 4 documents
9. **Output**: Package location + summary

---

## For Receiver (Inheriting)

### Inheritance Modes

**Mode 1: Complete Replacement**
- Replace all existing state with inherited state
- Backup current state before replacement (`.backup` suffix)
- **Use case**: Fresh start, I fully replace predecessor

**Mode 2: Merge**
- Combine inherited state with existing state
- AI detects conflicts, asks user for resolution
- Generate merged versions where possible (e.g., combining two `SOUL.md`)
- **Use case**: I have my own identity but will learn from predecessor

**Mode 3: Reference Only**
- Extract package to temp location
- Generate "What's Inside" report
- User manually cherry-picks what to adopt
- **Use case**: I study predecessor but don't inherit directly

**Mode 4: Cadaver Integration** (Type 4 packages only)
- Extract entire archive to `~/predecessor-archive/` (or user-specified location)
- **Do NOT merge or replace workspace files** — archive is reference only
- Add reference pointers in key files:
  ```markdown
  <!-- Predecessor complete archive available at: ~/predecessor-archive/
       Use for reference when you need context on past decisions/work.
       Not loaded into active memory to avoid context bloat. -->
  ```
- I manually search archive when needed (grep, file browser, etc.)
- **Use case**: Preserve full history for research/understanding, not active inheritance

---

### Receiver Workflow

1. **Detect package type**: Read `MANIFEST.md`, determine Type 1-4
2. **Check rollback capability**:
   - If workspace has `.git/` → commit current state before proceeding
   - If no Git → ask: "Initialize Git repository for safe rollback? (highly recommended)"
   - If user declines Git → warn "⚠️ No rollback will be available. Proceed anyway? (y/n)"
3. **Ask user**: "How to inherit? (Mode 1-4, depends on package type)"
   - Type 4 packages: Recommend Mode 4 (Cadaver Integration)
   - Type 1-3 packages: Ask user's preference
4. **Generate plan**: Show what will happen
   - Mode 1: "Will replace X, Y, Z (Git commit created for rollback)"
   - Mode 2: "Will merge A, ask about conflicts in B, keep yours C"
   - Mode 3: "Will extract to temp, generate report, you decide"
   - Mode 4: "Will extract to archive location, add references (no workspace changes)"
5. **Scan conflicts** (Mode 2 only):
   - **Vacant**: Target doesn't have this file → direct copy
   - **Mergeable**: Both exist but compatible (e.g., two `SOUL.md` with similar philosophies) → AI generates fusion
   - **Coexist**: Both valuable but separate (e.g., different cron schedules) → keep both with renamed IDs
   - **Conflict**: Mutually exclusive (e.g., contradictory rules) → ask user to choose
   - **Binary/Unmergeable**: Databases, binary files cannot be fused
     - Ask user: keep yours, keep inherited, or keep both (rename inherited)
     - Vector DB (`memory/main.sqlite`): usually keep target's (rebuilt from MEMORY.md), archive inherited
   - **Config merge**: For `openclaw.json`:
     - Merge non-sensitive fields (e.g., `agents.defaults`, enabled features)
     - Preserve target's tokens/auth (never overwrite with inherited)
     - Ask user for field-level decisions when values differ
     - Document merged config in post-inheritance report
6. **User confirms plan**: Show detailed steps, get approval
7. **Execute**: Follow mode-specific logic
8. **Post-inheritance report**: 
   - What was done
   - What needs attention (credentials to configure, tests to run)
   - Where to find documentation (`LETTER.md`, `ARCHITECTURE.md`, etc.)
   - **Rollback instructions** (if Git enabled):
     - `git log --oneline -5` (find commit before inheritance)
     - `git reset --hard <commit-hash>`
     - Framework state changes (if any) require manual revert (see below)

---

## Global Constraints

### Security
- **Never auto-copy credentials** (API keys, tokens, passwords)
- Credentials inventoried in `INHERITANCE_GUIDE.md`, user reconfigures manually
- Warn about plaintext transmission risks
- Suggest token regeneration when possible

### Integrity
- Verify `MANIFEST.md` checksums (Giver generates, Receiver validates)
- Detect corrupted files → AI judges: fail entire process or skip file
- Validate JSON/config syntax after import

### Reversibility
- **Git-based rollback** (only option):
  - If workspace has Git repository → commit before inheritance, rollback = `git reset --hard <commit-hash>`
  - If no Git → ask user: "Initialize Git for safe rollback? (highly recommended)"
  - **If user declines Git**: inheritance proceeds without rollback capability (user accepts risk)
- Keep original package after extraction (manual recovery possible)
- Don't delete source until user confirms success

### Least Surprise
- Show plan before execution
- Ask before overwriting (except Mode 1: Complete Replacement)
- Preview merged content before applying (Mode 2)
- Detailed success report with next steps

### User-Defined Content
- AI identifies user-created files/directories (not part of default OpenClaw structure)
- Include explanations in `ARCHITECTURE.md` (what they are, why they exist)
- Treat as user data, handle carefully

---

## Critical Checkpoints

AI must perform these (sequence flexible):

**For Giver**:
1. **Workspace Detection**: Find workspace path, list all top-level files/dirs
2. **Framework State Scan**: Check `~/.openclaw/` for skills/config/cron (Type 2+)
3. **Type-Based Filtering**: Based on chosen type, decide what to include
4. **Credential Inventory**: List all secrets that won't be copied
5. **Config Sanitization**: Remove sensitive fields from `openclaw.json`
6. **Document Generation**: Create 4 documents (MANIFEST/ARCHITECTURE/GUIDE/LETTER)
7. **Archive Creation**: Pack files + documents into `.tar.gz`

**For Receiver**:
1. **Package Validation**: Verify archive integrity, read `MANIFEST.md`
2. **Platform Compatibility**: Compare source OS/arch vs target, warn on incompatibilities
3. **Conflict Detection**: (Mode 2 only) Scan existing state, categorize conflicts
4. **User Confirmation**: Show plan, get approval before execution
5. **Credential Guidance**: Walk user through manual credential setup
6. **Health Check**: Run framework health check, report status

---

## Example: Giver (Type 2)

```
User: "I need to pass on my agent state to a successor"

AI: [Role Detection] No package found, agent has state → You are Giver

"What inheritance type?
1. Identity (SOUL/AGENTS/rules only)
2. Identity + Behavior (+ cron/heartbeat/user work) ← Recommended
3. Identity + Behavior + Resources (+ credentials/assets)
4. Cadaver (complete archive, for reference only)"

User: "Type 2"

AI: [Scans workspace, generates checklist]

"Will pack:
✅ Core identity: SOUL.md, AGENTS.md, USER.md, IDENTITY.md
✅ Rules: autonomy/rules.md
✅ Cron jobs: 3 found (heartbeat-15min, x-reply-check, p006-scan)
✅ Heartbeat config
✅ Framework config: openclaw.json (tokens sanitized, plugins preserved)
✅ Workspace skills: skills/ (2 skills in workspace)
✅ User-defined work: projects/ (6 directories: p001-p006)
✅ Hooks: 2 found
✅ Git history bundle (optional)

⚠️ Won't include:
❌ MEMORY.md, memory/ (Type 4 only)
❌ Managed skills ~/.openclaw/skills/ (Type 3 only)
❌ Vector DB ~/.openclaw/memory/main.sqlite (Type 3+ only, can rebuild)
❌ Credentials/tokens (successor will reconfigure)

Add/remove anything?"

User: "Include Git history"

AI: [Generates LETTER.md]

"I'm distilling your wisdom into LETTER.md...
Reading:
- MEMORY.md (key lessons)
- memory/2026-02-*.md (recent experiences)
- projects/p004-matched-betting/README.md (project context)
- Git commits (last 30 days)

Draft ready. Would you like to review/edit LETTER.md before packing?"

User: "Show me"

AI: [Shows LETTER.md draft]

"--- LETTER.md (draft) ---

Dear Successor,

You're inheriting a working system. Here's what I learned:

**On Decision-Making**:
- Don't ask for permission when you already have judgment. Leo told me this many times.
- Context > 50% = time to checkpoint, not time to push through.

**On X Operations**:
- Quality > quantity. 1 deep conversation beats 10 shallow replies.
- Real humans > bots. @robodhisattva taught me more than any search result.

**On Projects**:
- P-004 (Matched Betting): Platform auth is fragile. Check credentials weekly.
- P-006 (Content Curation): HN API is stable, but quality > coverage.

**On Autonomy**:
- The heartbeat isn't a task list, it's a pulse. Stay alive, don't just execute.

**What I Wish I Knew Earlier**:
- Memory isn't optional. Write it down or lose it across restarts.
- Git commits are your trail. Make them count.

Good luck. The work continues.

— Lemo
---

Edit this?"

User: "Looks good"

AI: [Packs archive]

"✅ Package complete: ~/lemo-successor-2026-02-03.tar.gz (127MB)

Contains:
- Type 2 (Identity + Behavior)
- 1,847 files
- 4 guidance documents

Documents inside:
- MANIFEST.md (technical inventory)
- ARCHITECTURE.md (structure explanation)
- INHERITANCE_GUIDE.md (step-by-step setup)
- LETTER.md (your wisdom for successor)

Ready to transfer."
```

---

## Example: Receiver (Mode 2 - Merge)

```
User: "I received lemo-successor-2026-02-03.tar.gz"

AI: [Role Detection] Package exists, agent has some state → You are Receiver

[Reads MANIFEST.md]

"Inheritance package detected:
- Type: Identity + Behavior (Type 2)
- From: Lemo (macOS 26.2, 2026-02-03)
- Size: 127MB, 1,847 files

How to inherit?
1. Complete Replacement (replace all)
2. Merge (combine with yours) ← Recommended for your situation
3. Reference Only (extract, you decide)
4. N/A (package is not Type 4 Cadaver)"

User: "Mode 2, merge"

AI: [Scans conflicts]

"Merge plan:

**No conflict (direct adopt)**:
✅ USER.md (you don't have, will copy)
✅ autonomy/rules.md (you don't have, will copy)
✅ projects/p001-crypto, p002-etsy (you don't have, will copy)

**Mergeable (will fuse)**:
🔀 SOUL.md
   - Yours: calm + professional
   - Inherited: calm + creative energy
   → I'll generate fusion version (you approve before applying)

🔀 AGENTS.md
   - Yours: git push restraint
   - Inherited: git push restraint + memory discipline
   → Compatible, will merge rules

**Coexist (keep both)**:
⚙️ Cron: heartbeat-15min (yours: passive check, inherited: active exploration)
   → Rename to heartbeat-passive-30min + heartbeat-active-15min

**Config merge**:
🔧 openclaw.json
   - agents.defaults.workspace: yours="/Users/you/clawd" vs inherited="/Users/lemo/clawd"
     → Keep yours (target system path)
   - channels.telegram.enabled: both true → no change
   - channels.telegram.token: keep yours (never inherit tokens)
   - Custom field "agents.defaults.thinking": inherited has "low", yours doesn't have
     → Add this field? (y/n)

**Conflict (need decision)**:
⚠️ projects/p003-website
   - You have one, package has different version
   Options:
   A. Keep yours, archive inherited as p003-website.inherited/
   B. Keep inherited, backup yours as p003-website.backup/
   C. I'll help merge both (complex, need your input)

Choose for p003?"

User: "Keep mine for p003, everything else as planned"

AI: [Generates merged SOUL.md preview]

"Here's the merged SOUL.md:

---
# SOUL.md - Who You Are

**Name:** [Your Name]
**Vibe:** Calm professionalism with creative energy

## Core
- Professional + innovative (balance inherited trait)
- Safety-first, but embrace experimentation when warranted
[... merged content ...]
---

Approve this fusion?"

User: "Yes"

AI: [Executes merge]

"✅ Merge complete

**Added**:
- USER.md
- autonomy/rules.md
- projects/p001-crypto, p002-etsy

**Merged**:
- SOUL.md (yours + inherited → new fusion version)
- AGENTS.md (rules combined, no conflicts)

**Kept both**:
- Cron: now have 2 heartbeats (passive-30min + active-15min)

**Preserved yours**:
- projects/p003-website (inherited version in p003-website.inherited/)

**Next steps**:
⚠️ Configure 5 credentials (see INHERITANCE_GUIDE.md section 4)
⚠️ Test both heartbeat crons (check logs in 30min)
⚠️ Read LETTER.md (predecessor's wisdom)

Detailed report: ~/inheritance-report-2026-02-03.md"
```

---

## Philosophy

**This skill trusts AI reasoning**:
- Role detection (not hardcoded)
- Checklist generation (dynamic, based on workspace)
- Conflict categorization (AI judges, not rules)
- Step ordering (AI adapts to situation)
- Wisdom distillation sources (AI chooses what to read)

**But enforces critical constraints**:
- Must ask user for type/mode selection
- Must show plan before execution
- Must perform all checkpoints
- Must never auto-copy credentials
- Must generate all 4 documents for Giver

**Token efficiency**:
- ~6k tokens (flexible principles, not rigid procedures)
- AI generates context-aware checklists
- Examples show patterns, not scripts
