# Meeting Intelligence Autopilot

A fully automated post-call pipeline for founders and sales teams. Every meeting ends the same way: action items routed, CRM updated, follow-up drafted, notes doc created, Slack DM sent. No manual work.

Built and running in production at TreasuryPath.

---

## What It Does

When a meeting ends on Fathom (or any meeting recorder with an API):

1. **Polls for new meetings** every 30 minutes via cron
2. **Classifies the meeting** - internal, prospect, client, partner, investor, advisor
3. **Extracts structured intel** via AI - commitments, deal signals, action items, follow-ups
4. **Routes outputs** based on classification and signal strength:
   - Basecamp todos for each action item (your items vs. their commitments labeled separately)
   - GitHub issues for engineering tasks from internal meetings
   - Gmail draft for high-signal prospect calls
   - Google Doc notes artifact for every external call
   - HubSpot deal note + next step updated automatically
5. **Slack DM** with signal score, top signals, and clickable links to everything

---

## Architecture

```
Fathom API
    ↓
backfill.py      — poll for new meetings, write to SQLite
    ↓
classify.py      — internal / prospect / client / partner / investor / advisor
    ↓
extract.py       — 2-pass AI extraction → intel JSON
    ↓
execute.py       — route outputs, update CRM, send Slack DM
```

Everything is driven by an intel JSON file per meeting. All outputs are idempotent - re-running never creates duplicates.

---

## AI Extraction Prompt (Pass 1 — Structured Intel)

```
You are a sales intelligence engine. Extract structured data from this meeting transcript.

Meeting: {title}
Classification: {classification}
Date: {date}

Return JSON with these fields:

commitments: Array of {who, what, deadline}
  - "who": "jeff" | "counterparty"
  - "what": the specific commitment made
  - "deadline": date string or null

deal_signals: Array of {type, content, confidence}
  - type: "next_step" | "objection" | "competitor" | "budget_signal" | "timeline" | "champion"
  - content: what was said
  - confidence: 0.0-1.0

followups: Array of {action, owner, missed_by_fathom}
  - missed_by_fathom: true if Fathom's auto-summary missed this item

competitor_mentions: Array of {name, context}
advisor_intros_offered: Array of {target, by, context}

Transcript:
{transcript}
```

---

## AI Extraction Prompt (Pass 2 — Output Decision)

```
Given this meeting intel, decide what outputs to generate.

Meeting: {title} ({classification})
Signal data: {intel_summary}

Decide:
- outputs: list from [email_draft, one_pager, basecamp_todos, github_issues]
- signal_score: 0.0-5.0 (deal momentum - buying signals weighted higher than next steps)
- reasoning: one sentence explaining the score

Rules:
- email_draft: signal_score >= 2.5 and classification is prospect/client/partner
- one_pager: any external call
- basecamp_todos: any meeting with commitments or action items
- github_issues: internal meetings with engineering action items

Return JSON.
```

---

## HubSpot Deal Hygiene (Not Meeting Logging)

Fathom already syncs meeting notes to HubSpot. This pipeline does the post-call work a human would normally do manually:

1. Fuzzy-match the company name against active deals (local cache)
2. Create a structured HTML note on the deal: signal score, key signals, your next steps, their commitments
3. Update `hs_next_step` on the deal with your first action item
4. Surface a stage change recommendation in Slack if signal score warrants it

If no deal is found (new prospect), skip gracefully.

---

## Action Item Format

Clean titles so you can read the todo list without clicking anything:

- **Your items:** `Acme: Follow up on pricing proposal`
- **Their commitments:** `Acme / THEM: Intro to VP Finance`

---

## Slack DM Format (Block Kit)

```
*TreasuryPath<>Acme Corp*  ·  Apr 17

prospect  ·  acme.com
---
✅ 2 Basecamp todos, Gmail draft, One-pager, HubSpot note → Acme Corp Deal
↑ Deal stage: Move to Presentation Scheduled? (signal 4.2)
---
📊 *Signals* — score 4.2
• Sarah committed to getting us on the shortlist
• Budget approved for Q3 pilot
• Competitor: Ramp mentioned but "too complex for our team"
---
Fathom  ·  Gmail Drafts  ·  One-pager  ·  HubSpot  ·  Reply *send* to post to #gtm-deals
```

---

## Deduplication

Every output stores its ID in the intel JSON. Safe to re-run any step:

| Output | Dedup field |
|--------|-------------|
| Basecamp todos | `basecamp_todo_urls[]` |
| Gmail draft | `gmail_draft_id` |
| Google Doc | `one_pager_url` |
| HubSpot note | `hubspot_note_id` |
| Full execute | `executed_at` |

---

## Scheduling (macOS)

```bash
# sync-meeting-intel.sh
LOCK=/tmp/meeting-intel.lock
[ -f "$LOCK" ] && exit 0
touch "$LOCK"
trap "rm -f $LOCK" EXIT

export FATHOM_API_KEY=$(grep FATHOM_API_KEY ~/.config/fathom/.env | cut -d= -f2)
python3 /path/to/run_pipeline.py
```

Cron entry:
```
PATH=/usr/bin:/bin:/usr/sbin:/sbin:/opt/homebrew/bin:/home/user/.local/bin
0,30 * * * * /path/to/sync-meeting-intel.sh >> /path/to/meeting-intel.log 2>&1
```

Note: On macOS, `flock` does not exist. Use the shell lockfile pattern above.

---

## What You Need

- **Fathom** (or any meeting recorder with an API) - transcript + share URL
- **OpenAI / Anthropic / LiteLLM** - AI extraction
- **Slack bot** - `chat:write` scope for DMs
- **HubSpot private app** - CRM read + notes write + deals write
- **Google OAuth** - Gmail compose scope + Docs scope
- **Basecamp CLI** - authenticated for todo creation
- **gh CLI** - authenticated for GitHub issue creation
- **SQLite** - no server needed, single file database

---

## What It Doesn't Do

- Change deal stages automatically (that's a judgment call - surfaces the recommendation instead)
- Log the meeting to HubSpot (Fathom handles that)
- Process meetings in real-time (polls every 30 min - good enough)
- Replace your CRM workflow (it feeds it, not replaces it)

---

## Key Design Decisions

**Why JSON files per meeting, not just the database?**
The intel JSON is the unit of work. All stages read and write to it. Makes debugging easy - you can inspect exactly what was extracted and why each output was or wasn't generated.

**Why cron instead of webhooks?**
Fathom can't reach localhost. Cron is simpler, more reliable, and easy to debug. 30-minute latency is fine for post-call follow-up.

**Why not auto-update deal stage?**
Signal scores are probabilistic. A founder should make the call on whether a conversation actually moved a deal forward. The pipeline gives you the data, you make the decision.

**Why not just use HubSpot's native meeting sync?**
Fathom syncs raw notes. This pipeline does the structured extraction - separating your commitments from theirs, scoring deal momentum, generating the follow-up draft. That's the human work it replaces.
