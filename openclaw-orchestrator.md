# OpenClaw as a CLI Orchestrator

How to configure your OpenClaw agent as an intelligent orchestrator of specialist CLIs — Claude Code, Gemini, Codex, and others — rather than doing all work directly.

**Reviewed by Opus and Qwen before publishing.** The first draft of this pattern had real problems that the review caught. The corrected version is below.

---

## The Core Idea

Your OpenClaw agent is the conductor. Specialist CLIs are the musicians. The agent decides *what* gets done and *who* does it — it doesn't do complex multi-file work itself when a CLI can reason about it better.

```
OpenClaw agent (orchestrator)
├── Claude Code  → agentic tasks: multi-file reasoning, judgment, iteration
├── Gemini       → true large context: 100K+ tokens, massive docs
├── Codex        → fallback if Claude Code unavailable
└── Agent direct → everything else (the default)
```

**The default is always: do it directly.** Delegate only when there's a concrete reason.

---

## What the Reviews Caught (Don't Make These Mistakes)

Before getting to the implementation, here's what went wrong in the first version of this pattern — flagged by running it through a multi-model review:

### 1. Claude Code vs. Codex routing is mostly theater
Both are agentic coding CLIs. The behavioral difference is real but not stable enough to route on — it shifts with every model update. The real split is **agentic** (needs tools, file access, iteration) vs. **generative** (single-shot output). That's vendor-agnostic and durable.

**Fix:** Pick one as primary (Claude Code), use the other as a fallback, not a parallel route.

### 2. Threshold-based routing breaks at the boundary
"5+ files," "100K tokens," "30 seconds" sound clean but fail in practice:
- You can't know how many files a task touches until after you've explored them
- Thresholds create inconsistent behavior for tasks near the boundary
- Token count is a poor proxy for complexity (200K tokens of boilerplate < 50K tokens of dense logic)

**Fix:** Route on *capability requirements* (needs multi-file judgment? needs iteration? needs true large context?) not on quantity thresholds.

### 3. No failure handling = the system is less reliable than doing it directly
If a CLI times out, returns garbage, or partially completes, an orchestration system without error handling silently produces bad state. Cascading failures where no human sees the original error are worse than the agent doing the task itself.

**Fix:** Define the expected output before delegating. Validate before accepting. On failure, fall back to agent direct.

### 4. "Flat-rate subscription = delegate freely" ignores real costs
Every delegation adds: routing judgment, prompt engineering, CLI cold start (2-15s), output parsing, validation. For a task the agent could do in 10 seconds, delegation may take 45+ seconds. Flat-rate saves tokens, not time.

**Fix:** Delegate where the task is genuinely complex and benefits from the CLI's capabilities — not speculatively.

---

## The Routing Rules (Corrected)

### Route to Claude Code when:
- Task requires exploring files before acting (not just reading one)
- Multi-file work where content judgment matters (not mechanical find/replace)
- Work where partial completion would corrupt state
- Multi-step tasks needing tools + iteration, not just generation

### Route to Gemini when:
- Input genuinely exceeds 100K tokens (entire codebase, massive contract sets)
- Synthesizing many long documents where a 200K context window isn't enough

### Use Codex when:
- Claude Code is unavailable or rate-limited
- As a fallback, not a parallel route

### Agent direct (the default):
- Single file edits or reads
- Simple shell commands
- Quick API calls
- Anything a generalist handles fine

---

## Failure Handling (Required, Not Optional)

Before every delegation:

```
1. Define expected output format (schema, required fields, success criteria)
2. Spawn the CLI with a clear task and output spec
3. Validate the output against the spec before accepting
4. On failure → agent direct, log what failed and why
5. Never assume success — parse and verify
```

Without this, you don't have an orchestration system. You have hope-based routing.

---

## Observability

Log every delegation:
- Which CLI, what task type, success or fail, duration
- Review weekly: is delegation actually helping or adding overhead?
- Drop routes that consistently underperform agent direct

Without observability, the routing logic never improves and you can't tell when it's broken.

---

## How to Spawn Claude Code from OpenClaw

```bash
# Foreground (wait for result)
cd ~/your-workspace && claude --permission-mode bypassPermissions --print 'Your task here'

# Background (fire and check later)
cd ~/your-workspace && claude --permission-mode bypassPermissions --print 'Your task here' &
```

**Good prompt structure for Claude Code:**

```
Context: [2-3 sentences about what exists and what matters]

Task: [What to do — be specific about scope and constraints]

Expected output: [What done looks like — files modified, format of response, success criteria]

Rules: [Any hard constraints — don't delete X, don't touch Y, always ask before Z]
```

---

## How to Use Gemini for Large Context

```bash
# Direct question
gemini --model gemini-2.5-pro 'Question here'

# With file input
cat large-document.md | gemini --model gemini-2.5-pro 'Summarize and extract action items'

# Multiple files
cat file1.md file2.md file3.md | gemini --model gemini-2.5-pro 'Find inconsistencies across these'
```

---

## The SOUL.md Pattern

If you're using OpenClaw with a `SOUL.md` or system prompt, add an orchestration section like this:

```markdown
## CLI Orchestration

Default: do it directly. Delegate when the task genuinely benefits from a specialist CLI.

Routes:
- Claude Code → agentic multi-file work needing content judgment
- Gemini → true large context (100K+ tokens)
- Agent direct → everything else

Before delegating:
1. Define expected output format
2. Validate result before accepting
3. On failure → agent direct, log it

Observe: log every delegation. Drop routes that don't earn their overhead.
```

---

## When NOT to Use This Pattern

- **You don't have production data on where the agent struggles.** Build routing from evidence, not theory. Start with agent direct for everything. Delegate only where you've repeatedly hit real limitations.
- **Tasks are short and mixed.** Real work doesn't arrive pre-categorized. "Review this PR, fix the two obvious bugs, and write a standup summary" needs three different routes stitched together. Complexity multiplies.
- **You want the appearance of thoroughness.** Running three CLIs on a task that one agent handles fine wastes time and produces coordination overhead, not better results.

---

## Starting Point

1. Run everything through the agent directly for 2-4 weeks
2. Note where it's consistently slow, wrong, or produces low-quality output
3. Delegate *only those specific task types* to the appropriate CLI
4. Measure: is the output better? Is it worth the overhead?
5. Add a second route only after the first one is validated

The orchestration layer should grow from evidence. A three-CLI routing diagram written before you have production data is premature architecture that becomes a maintenance burden.

---

*Part of the [prompts](https://github.com/jforksy/prompts) collection.*
