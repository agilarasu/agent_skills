---
name: smart-commit
description: >
  Use this skill when the user wants to commit staged code, create a git commit, write a commit message, or run a pre-commit check. Trigger on phrases like "commit this", "ready to commit", "commit my changes", "write a commit message", "check my staged files", "smart commit", or just "commit". Also trigger when the user says they're done with a feature or fix and wants to push it. This skill checks staged changes, runs safety checks, asks the user clarifying questions if needed, generates a commit message following conventional commit format, and executes the commit — all in one flow.
---

# Smart Commit Skill

A skill that acts like a senior dev doing a last-minute sanity check before committing — catches obvious issues, fills in context gaps, writes a good commit message, and commits.

## Core Philosophy

Most developers (especially vibe coders) commit too fast or too slow. Too fast = production breaks from unreviewed code. Too slow = context loss. This skill creates a lightweight but meaningful checkpoint between "done" and "committed."

The goal is not a full code review. It's a **30-second gut check** that catches the most common mistakes and produces a commit message that actually explains what changed and why.

---

## The Flow

### Phase 1: Read the Staged Diff

Run these commands first to understand what's staged:

```bash
git diff --staged --stat          # file summary — what changed
git diff --staged                 # full diff — what actually changed
git status                        # any unstaged or untracked files worth knowing about
```

If `git diff --staged` returns empty → tell the user "Nothing is staged. Run `git add` first." and stop.

Also check:
```bash
git log --oneline -5              # recent commit history — for tone/format consistency
git branch --show-current         # current branch name — helps infer context
```

---

### Phase 2: Automated Checks

Run these checks silently and flag any that fail. Don't block the commit — just surface findings.

#### 2a. Obvious Issues
- **Console logs / debug prints**: grep staged diff for `console.log`, `print(`, `debugPrint(`, `Logger.d(`, `TODO`, `FIXME`, `HACK`, `XXX`
- **Hardcoded secrets smell**: grep for `password =`, `secret =`, `api_key =`, `token =`, `Bearer `, `sk-`, `AIza` — flag if found, never commit these
- **Large files**: check if any staged file is >500 lines changed — flag for review
- **Test files missing**: if `.dart`, `.py`, `.ts`, `.js` files changed but no test files staged, note it (don't block)

#### 2b. Branch Awareness
- If on `main` or `master` → warn: "You're committing directly to main. Intentional?"
- If branch name matches a pattern like `fix/`, `feat/`, `hotfix/` → use that context in the commit message

---

### Phase 3: Ask the User (Max 3 Questions)

Only ask what you genuinely can't infer from the diff. Keep it tight — max 3 questions, presented together, not one by one.

**Always ask:**
1. "What's the reason for this change?" — unless it's obvious from the diff (e.g., a single renamed variable)

**Ask only if unclear from diff:**
2. "Is this complete, or a WIP/partial?" — if the diff looks incomplete or has TODOs
3. "Anything that could break from this?" — if the change touches shared utilities, APIs, configs, or DB schemas

Format the questions as a numbered list so the user can answer inline like "1. fixed the login bug 2. complete 3. might affect auth flow"

---

### Phase 4: Generate Commit Message

Use **Conventional Commits** format:

```
<type>(<scope>): <short summary>

<body — optional, only if needed>

<footer — optional, breaking changes or issue refs>
```

**Types:**
- `feat` — new feature
- `fix` — bug fix
- `refactor` — code change that doesn't fix a bug or add a feature
- `chore` — tooling, deps, configs
- `docs` — documentation only
- `style` — formatting, no logic change
- `test` — adding or fixing tests
- `perf` — performance improvement
- `ci` — CI/CD changes

**Scope:** infer from the files changed (e.g., `auth`, `api`, `ui`, `db`, `flutter`, `models`)

**Rules for the summary line:**
- Max 72 characters
- Imperative mood: "add feature" not "added feature"
- No period at the end
- Specific — avoid generic words like "update", "fix stuff", "changes"

**Body (include if):**
- The *why* is not obvious from the summary
- There's a breaking change
- Multiple unrelated things changed (list them)
- User mentioned something that could break

**Generate 2 options** — one concise, one with body — and ask: "Which one, or edit?"

Example:
```
Option 1 (concise):
fix(auth): handle null token on session expiry

Option 2 (with context):
fix(auth): handle null token on session expiry

Previously the app crashed silently when the session token
expired mid-request. Added null check before API calls and
redirect to login on 401.

Which one? (1/2/edit)
```

---

### Phase 5: Commit

Once the user confirms the message:

```bash
git commit -m "<first line>" -m "<body if any>"
```

After committing, run:
```bash
git log --oneline -1
```

And confirm to the user: the commit hash, the message, and how many files/lines changed.

Also ask: "Want to push?" — if yes, run `git push`. If the branch has no upstream, suggest `git push -u origin <branch>`.

---

## Flags / Options

Users can invoke with optional flags:

| Flag | Behavior |
|------|----------|
| `--dry-run` | Run all checks and generate message but don't commit |
| `--no-checks` | Skip Phase 2 automated checks, go straight to questions |
| `--push` | Auto-push after committing without asking |
| `--wip` | Prefix message with `wip:` and skip the questions |
| `--amend` | Use `git commit --amend` instead of a new commit |

---

## Edge Cases

### Nothing staged but there are unstaged changes
Show the unstaged files and ask: "Want to stage everything (`git add .`) or specific files?"

### Merge conflicts markers found in diff
Stop immediately: "There are unresolved conflict markers in the staged files. Resolve those before committing."

### First commit on repo
Skip the `git log` step. Use `chore: initial commit` as default suggestion.

### Monorepo / multiple packages changed
Note all affected packages in the scope: `fix(auth,api): ...` or suggest splitting into multiple commits.

### User says "just commit" or "yolo commit"
Respect it — skip questions (Phase 3), still run secret/debug checks (Phase 2a), generate a single commit message option, and commit.

---

## Tone

- Fast and direct — this is a workflow tool, not a tutor
- Flag issues clearly but don't lecture
- If something looks risky, say it once, don't repeat
- The user is the decision-maker — surface info, don't gatekeep
