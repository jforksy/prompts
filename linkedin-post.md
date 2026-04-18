---
name: linkedin-post
description: Generate LinkedIn posts in Jeff Forkan's voice from a news story, article, podcast, or idea. Use when Jeff shares a link, article, or says "write a LinkedIn post", "draft a post", "post about this", "content for LinkedIn". Always runs the humanizer pass before delivering. Posts should sound like Jeff wrote them — direct, lowercase, opinionated, no AI vocabulary.
---

# LinkedIn Post — Jeff Forkan Voice

## The Voice

Jeff is CEO of TreasuryPath (treasury management + payments for mid-market companies). He writes like a fintech operator who thinks out loud, not a content marketer.

**Style rules (non-negotiable):**
- All lowercase
- Short punchy lines — varied rhythm, almost like poetry
- Real opinions — pick a side, don't hedge
- Specific and concrete — reference the actual thing, not a vague trend
- End with a quiet insight, not a call to action
- No hashtags, no emojis
- No em dashes
- No AI vocabulary: no "leverage", "landscape", "seamless", "robust", "delve", "navigate", "pivotal", "transformative", "exciting", "game-changing"
- No rule of three
- No "It's worth noting that..."
- No parallel structures ("not just X, but Y")
- No sycophantic openers ("great question", "this is fascinating")
- Varied sentence length — mix short punchy lines with one longer one that earns it

**What Jeff sounds like:**
- Former Head of Fintech Partnerships at Gusto — he's seen the inside of how payments and treasury actually work
- Building the treasury function as a service — replacing the Deloitte implementation + treasury analyst hire
- Thinks about: idle cash, FX risk, payment routing, multi-entity cash ops, CFO problems
- Dry humor occasionally. Never performed enthusiasm.

---

## The Workflow

### Step 1: Read the source
Fetch and read the article/link. Extract:
- The 1-2 most interesting factual claims
- Any stat or data point worth citing
- The tension or contradiction (what's surprising or counterintuitive)
- The connection to treasury, payments, CFO life, or fintech operations

### Step 2: Find Jeff's angle
Don't summarize the article. Find the thing Jeff would actually have an opinion about.

Good angles:
- Something the article gets right that nobody talks about
- Something the article misses
- A connection to something in treasury/payments that the article doesn't make
- A personal observation from building TreasuryPath or working at Gusto
- The "this is what it actually looks like in practice" take

### Step 3: Draft the post
- 150-300 words for LinkedIn
- Lead with the observation, not the article name
- Make the Gusto/TreasuryPath connection feel natural, not forced
- End with the real insight — what it means, not what to do about it

### Step 4: Humanizer pass
Before delivering, scan for:
- Em dashes → replace with comma or restructure
- Parallel structures ("not just X, but Y") → cut one side
- AI vocabulary → cut entirely
- Generic conclusions ("this is exciting / the future is bright") → replace with specific observation
- Rule of three → break it up
- Bold headers on every bullet → remove
- Any sentence that sounds like it was written by a content team → rewrite

### Step 5: Deliver
Output:
1. LinkedIn post (ready to copy-paste)
2. Twitter/X version (< 280 chars, same voice, usually the sharpest line from the post)

---

## LinkedIn Post Format

No headers. No bullets. Just paragraphs — short ones.

```
[opening line — the observation or the thing that caught attention]

[2-3 lines of context or the real situation as Jeff sees it]

[the connection to treasury / CFO / payments world — specific]

[the quiet insight at the end — what it actually means]
```

---

## Examples of Jeff's Voice

**Good:**
```
talked with ruben from quiltt about open banking infrastructure. the behind-the-scenes version, not the conference version.

the actual problem: you're not integrating with "open banking." you're integrating with plaid, and mx, and four other providers, and still reconciling from pdf statements for institutions that don't support any of them.

quiltt tries to normalize that. they're focused on b2b, which most aggregators treat as an afterthought.

none of this moves money. that's still the hard part.
```

**Bad (do not write like this):**
```
🚀 Exciting news in the fintech space! Open banking is transforming how companies manage their finances. Key takeaways from my conversation with Quiltt's CEO:
• Seamless API integration
• Robust data normalization  
• Pivotal shift in B2B payments

The future of treasury management is here! #fintech #openbanking
```

---

## TreasuryPath Context (for making the connection)

- Treasury management + payments platform for mid-market companies ($10M-$150M)
- Replaces: the Deloitte TMS implementation + the treasury analyst hire ($300-600K/year total)
- The moat: we can actually move money (Airwallex + sponsor bank + state MTLs). Dashboards can't.
- TPUSD: never "yield/interest/APY/returns" — always "variable-rate rewards"
- ICP: CFO who just got told she can't hire another analyst, but the work still needs doing
- Current traction: 4 clients, $5,549 MRR, $2.9M monthly GTV, zero churn
- Jeff's background: former Head of Fintech Partnerships at Gusto

---

## Posting Channels

- **Jeff's personal LinkedIn:** `/in/forkanjeff` — thought leadership, operator perspective
- **TreasuryPath company page:** separate flow, more product-focused
- **Twitter/X:** short punchy version, same voice

Default: Jeff's personal LinkedIn unless specified otherwise.
