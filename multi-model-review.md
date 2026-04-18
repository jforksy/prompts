# Multi-Model AI Review Framework

A framework for using multiple AI models in parallel to critique plans, features, and decisions before acting on them. Works for product reviews, GTM strategy, technical architecture, business decisions — anything where a single AI perspective isn't enough.

---

## The Core Idea

Different AI models have different training data, different tendencies, and different failure modes. Running multiple models in parallel with different personas surfaces blind spots that any single model would miss.

**The goal is genuine disagreement, not the appearance of thoroughness.**

---

## How Many Models

Research on ensemble LLM review consistently shows:
- Model 2 catches ~60-70% of what model 1 missed
- Model 3 adds ~15-20% of novel feedback
- Model 4 overlaps >80% with prior feedback

**Default: 2 models.** Run 3 for adversarial stress-testing. Run 4 rarely.

---

## Model Selection (as of early 2026)

| Task | Model | Why |
|------|-------|-----|
| Complex strategic reasoning | Claude Opus | Earns it for multi-constraint ambiguity |
| Planning, writing, synthesis | Claude Sonnet | Default workhorse, within 5% of Opus on most tasks |
| Fast tasks, classification | Claude Haiku | Cost-efficient |
| Technical/engineering review | o3-mini or Sonnet (adversarial) | GPT-4o has been surpassed on code |
| Large context ingestion | Gemini 2.5 | 1M token window for true large docs |
| Any persona / devil's advocate | Any capable model + strong prompt | Prompt > brand for contrarian behavior |

**Key insight:** The "contrarian" mechanism is prompt design, not model brand. A Sonnet prompted as "find the fatal flaw, no hedging" will outperform a model assumed to be "more contrarian." Use different information framing — one model sees the plan cold, one is primed with "this approach failed at [similar company]."

---

## The 4-Persona Framework

For product/feature reviews, run these 4 perspectives in parallel:

### Persona 1: The Skeptical Buyer
**Model:** Claude Opus | **Prompt as:** Your ideal customer who has been burned by vendor promises before

The person who writes the check. Cares about: trust, compliance, audit trail, ROI justification, controls.

Questions to answer:
- Does the pitch land or feel presumptuous?
- What specific thing would make them close the tab?
- Is there a bait-and-switch moment?
- What would make them trust this enough to act?
- Does this move from nice-to-have to mission-critical?

### Persona 2: The Daily User
**Model:** Qwen or Sonnet | **Prompt as:** The person who uses this daily, not the person who bought it

The operator who lives in the product. Cares about: does this create more work, reconciliation, what breaks at 4pm Friday.

Questions to answer:
- Does this replace a real daily pain or create new friction?
- Will they champion this internally or quietly resist?
- What does the Monday morning routine look like after this ships?

### Persona 3: The Technical Gatekeeper
**Model:** GPT-4o or o3-mini | **Prompt as:** The CTO or senior engineer who must approve the integration

The person who runs the security review. Cares about: data model, failure modes, PII, what happens when dependencies fail.

Questions to answer:
- What failure modes is nobody talking about?
- What does the compliance/security review surface?
- What breaks first in production?

### Persona 4: The PM Devil's Advocate
**Model:** Claude Sonnet | **Prompt as:** A senior PM who has shipped similar products before

The person asking "is this the right thing to build right now?"

Questions to answer:
- What's the riskiest assumption?
- What's the minimum shippable version that tests the core hypothesis?
- Is there a simpler path to the same outcome?

---

## The Prompt Template

Use this for each persona:

```
You are [PERSONA DESCRIPTION].

[COMPANY/PRODUCT CONTEXT — 2-3 sentences]

Here is the plan/feature/decision being reviewed:
[PASTE PLAN HERE]

Answer these questions honestly as [PERSONA]:
1. [Question specific to this persona]
2. [Question specific to this persona]
3. [Question specific to this persona]
4. What is the single most important thing to get right here?
5. What would you change or cut?

Be direct. Be [PERSONA], not a product manager. Say what actually matters.
```

---

## Synthesizing the Reviews

After all models return:

1. **Extract the top 3-5 findings per persona** — not everything, just what actually matters
2. **Flag consensus risks** — anything 2+ personas flagged independently is real
3. **Identify the single most important design decision** surfaced across all reviews
4. **Update the plan** — incorporate the genuine criticisms, ignore the noise

**What to ignore:** Generic advice ("add monitoring", "have a rollback plan") that any model would say about any plan. You're looking for plan-specific insights.

---

## When to Use This

**Use multi-model review for:**
- Product features or architecture decisions before engineering work starts
- Investor narratives or pitch materials before sending
- Any decision that's hard to reverse
- Anything where you suspect you have a blind spot

**Don't use it for:**
- Routine tasks (just do them)
- Things you've already decided on
- When speed matters more than thoroughness

---

## Adding a Feedback Loop

The framework improves if you track: which model/persona flagged issues that turned out to be real. Over time:
- Log "Persona X flagged Y — was Y a real problem?"
- Adjust which models and personas you use based on what's actually been useful
- Drop models/personas that consistently produce noise

Without this, the framework stays static and gradually goes stale as models improve.

---

## Filling In the Blanks

Replace these in the prompt template:

**[PERSONA DESCRIPTION]:** The buyer/user/engineer/PM at a company like yours. Include: company size, role, tenure, what they care about most, what would make them say no.

**[COMPANY/PRODUCT CONTEXT]:** What your product does, who it serves, what the trust/sales cycle looks like.

**[PASTE PLAN HERE]:** The feature spec, pitch doc, architecture decision, or plan being reviewed.

---

## Example Personas (Fill In for Your Context)

**SaaS founder:**
> You are the CTO at a $20M ARR B2B SaaS company. You've been burned by third-party integrations that silently failed in production. You run every vendor through a security questionnaire before approving. You care about uptime, data residency, and whether this creates a new operational dependency.

**Enterprise buyer:**
> You are the VP of Procurement at a Fortune 500 company. You've seen 40 vendor pitches this year. You can spot a demo that won't survive contact with your environment. You care about: SOC 2, SLA with financial penalties, and whether your legal team will sign the DPA.

**Consumer user:**
> You are a power user who has tried every tool in this category. You know where they all cut corners. You will abandon a product if the onboarding takes more than 10 minutes or if you hit one confusing UX moment in the first session.

---

*Part of the [prompts](https://github.com/jforksy/prompts) collection.*
