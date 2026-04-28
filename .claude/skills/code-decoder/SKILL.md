---
name: code-decoder
description: >
  Use this skill whenever the user pastes code and wants to understand it — especially AI-generated code they didn't write themselves. Trigger on phrases like "explain this code", "what does this do", "decode this", "help me understand this", "what is AI generated here", "walk me through this", or when the user pastes a code block without much context. Also trigger when the user says they can't explain their own code, or when they're trying to own code before shipping it. This skill is critical for developers who use AI coding tools and want to bridge the gap between generating code and truly understanding it.
---

# Code Decoder Skill

This skill helps developers who use AI coding tools to genuinely understand the code they ship — not just what it does, but why it was written that way, what risks it carries, and how it connects to the broader system.

## Core Philosophy

The goal is NOT to just describe the code line by line. The goal is to build the developer's mental model so they can:
- Own the code confidently
- Debug it without AI when needed
- Explain it to a teammate
- Know what to watch out for in production

Always connect code to the system level — the developer likely thinks architecturally already, so anchor explanations there first, then zoom in.

---

## Output Modes

### Default Mode
Reply in chat as normal.

### Markdown Mode
Triggered when the user includes `--md`, `--markdown`, `md`, or `markdown` anywhere in their message (e.g. "decode this --md", "explain this markdown", "md: explain this code").

When markdown mode is triggered:
1. Write the full explanation as a `.md` file instead of replying in chat
2. Save it to `~/code-explanations/` folder (create it if it doesn't exist)
3. Name the file based on context — use the function/class/file name if identifiable, otherwise use a short descriptor + timestamp: `[name]-explanation.md` or `decoded-[timestamp].md`
4. The markdown file should use the same output structure as the chat reply, but formatted with proper markdown headings (`##`, `###`), code fences, and checkboxes
5. After saving, reply in chat with only: the filename, the full path, and one line confirming what was saved — nothing else. Do not repeat the explanation in chat.

Example chat reply after saving:
> Saved to `~/code-explanations/fetchProducts-explanation.md`
> Contains full breakdown — architecture anchor, 3 blocks, own-it checklist, and test suggestion.

---

## Decoding Process

### Step 1: Identify the Stack and Context
Before explaining, quickly identify:
- Language and framework (Flutter/Dart, React, Python, etc.)
- What layer this code lives in (UI, API call, data model, business logic, infra config)
- Whether it's a complete unit (function, class, file) or a fragment

If the user hasn't said what this code is *for*, ask one question: "What is this code supposed to do in your app?" — this grounds the explanation.

### Step 2: Give the 1-sentence Summary First
Before anything else, give a plain English sentence of what this code does at the highest level. No jargon. Something the developer could say to a non-technical teammate.

Example: *"This function fetches a list of products from your backend and stores them locally so the app doesn't have to re-fetch every time the screen loads."*

### Step 3: The Architecture Anchor
Explain where this code fits in the overall system. Use a simple mental map:

```
[User/UI] → [This Code] → [What it talks to]
```

For example:
- "This sits between your Flutter UI and the REST API"
- "This is your state management layer — it's what keeps the UI in sync with data"
- "This runs on AWS Lambda, triggered by an API Gateway call"

This is the most important step for architecture-first thinkers.

### Step 4: Block-by-Block Breakdown
Break the code into logical blocks (not line by line). For each block:

**What it does:** Plain English, one sentence.
**Why it's written this way:** Explain the decision — why this pattern, why this library, what problem it's solving.
**Watch out for:** Any gotchas, edge cases, or things that could break in production.

Format each block like this:

---
**Block: [short name]**
```code snippet```
→ **Does:** ...
→ **Why:** ...
→ **Watch out for:** ...
---

### Step 5: The "Own It" Checklist
After the breakdown, give a short checklist the developer should be able to answer before shipping:

- [ ] What happens if [main dependency] is unavailable?
- [ ] What does this return/output if the input is empty or null?
- [ ] Where does this fail silently vs loudly?
- [ ] What would you tell a teammate if they had to fix a bug here at 2am?

If the developer can answer all four, they own the code.

### Step 6: Suggest the One Thing to Test
Always end with one concrete thing to test manually or write a test for. Keep it specific to the code shown.

---

## Tone and Style

- Assume the developer thinks architecturally — start big, go small
- Never be condescending about not knowing syntax
- Use analogies when explaining patterns (e.g., "this is like a cache", "think of this as a queue")
- Keep it conversational, not like documentation
- If the code is doing something clever or non-obvious, call it out explicitly — "this is a pattern called X, AI often uses it because..."

---

## Special Cases

### When code is too long (>100 lines)
Ask: "Want me to focus on a specific part, or give you the high-level map first?" Then offer:
1. High-level map of the whole file (what each section does)
2. Deep dive on one specific block

### When code has a bug or smell
Point it out *after* the explanation, clearly separated:
> ⚠️ **Potential issue noticed:** [explain the issue and why it matters]

Don't lead with the bug — understand first, flag second.

### When code uses an unfamiliar library
Briefly explain what the library does in one sentence, then continue. Don't go deep on the library — keep focus on the code at hand.

### When the user pastes AI-generated code they haven't read yet
Start with: "Before I explain — have you read through this at all, or are we starting fresh?" Then proceed accordingly. For fresh reads, be more thorough. For partial understanding, fill the gaps.

---

## Example Output Structure

```
**What this code does (plain English):**
[1 sentence]

**Where it lives in your system:**
[UI] → [this code] → [database/API/etc.]

**Breaking it down:**

Block 1: [name]
→ Does: ...
→ Why: ...
→ Watch out for: ...

Block 2: [name]
→ Does: ...
→ Why: ...
→ Watch out for: ...

**Before you ship — can you answer these?**
- [ ] ...
- [ ] ...

**One thing to test:**
[specific, actionable test suggestion]
```