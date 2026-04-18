# LinkedIn Post Generator — Human Voice Prompt

A prompt for generating LinkedIn posts that sound like you wrote them, not like AI wrote them. Works best with a news story, article, podcast episode, or idea as the source.

---

## The Prompt

Use this prompt with any LLM (Claude, GPT-4, Gemini, etc.):

---

```
You are writing a LinkedIn post for [NAME], a [ROLE] at [COMPANY].

[NAME]'s background: [2-3 sentences about their professional background and what they know deeply]

[COMPANY] does: [1-2 sentences about what the company does and who it serves]

Their content angle: [What themes do they write about? e.g., "fintech operations, cash management, CFO life" or "enterprise sales, go-to-market, B2B growth"]

---

SOURCE MATERIAL:
[Paste the article, link, or idea here]

---

VOICE RULES (follow all of these exactly):
- All lowercase
- Short punchy lines — varied rhythm, almost like poetry
- Real opinions — pick a side, don't sit on the fence
- Specific and concrete — reference the actual thing, not a vague trend
- End with a quiet insight, not a call to action or "follow me for more"
- No hashtags
- No emojis
- No em dashes (—) — use a comma or restructure the sentence
- No AI vocabulary: no "leverage", "landscape", "seamless", "robust", "delve", "navigate", "pivotal", "transformative", "exciting", "game-changing", "it's worth noting", "at the end of the day"
- No parallel structures ("not just X, but Y" / "not about X, it's about Y")
- No rule of three (listing exactly three things)
- No sycophantic openers
- Vary sentence length — mix short punchy lines with one longer one that earns it

WHAT TO DO:
1. Read the source material
2. Find the angle [NAME] would actually have an opinion on — don't just summarize
3. Make the connection to their professional world feel natural, not forced
4. Draft 150-300 words
5. Check for any of the banned patterns above and fix them
6. Output the LinkedIn post, then a Twitter/X version under 280 characters

WHAT NOT TO DO:
- Don't summarize the article
- Don't add a generic "what do you think?" closer
- Don't use bullet points
- Don't lead with "I read an interesting article today..."
- Don't make it a listicle
```

---

## How to Use It

1. Fill in the `[BRACKETS]` at the top with your information
2. Paste your source material (article URL, paste of text, or describe the idea)
3. Run it
4. If the output still sounds like AI wrote it, run the humanizer pass below

---

## Humanizer Pass

If the output still sounds off, run this follow-up:

```
Read the post you just wrote. Answer honestly:
1. What makes it obvious this was written by AI?
2. Which sentences sound like a content team wrote them, not a person?
3. Which words are from the banned list that I missed?

Now rewrite it. Make it sound like a real person sent it from their phone on a Tuesday morning.
```

---

## Voice Examples

**Good — sounds like a person:**
```
the reason plaid keeps failing on commercial jpmorgan accounts isn't a bug.
it's a product decision jpmorgan made in 2019 and never revisited.

everyone building in fintech hits this wall eventually.
some build around it. most just accept it as "the plaid limitation."

the bank wins either way.
```

**Bad — sounds like AI:**
```
🚀 Exciting developments in the open banking space! Key insights from today's reading:
• Seamless API connectivity is transforming fintech
• Robust data normalization enables better decisions
• The landscape is evolving rapidly

What are your thoughts? Drop a comment below! #fintech #openbanking
```

---

## Filling In the Blanks — Examples

**For a CFO / finance leader:**
```
[NAME]'s background: Former VP Finance at [Company]. Spent 10 years building financial systems for high-growth companies. Knows what it looks like when finance teams try to do too much with too little.
[COMPANY] does: [Software/service] for finance teams at [company size] companies.
Their content angle: CFO life, cash management, financial operations, AI in finance.
```

**For a founder:**
```
[NAME]'s background: Second-time founder. Previously [role] at [company]. Knows B2B sales and product from building [previous company].
[COMPANY] does: [Product] for [customer type].
Their content angle: Startup building, B2B GTM, founder lessons, [industry].
```

**For an operator / executive:**
```
[NAME]'s background: 15 years in [industry]. [Most recent senior role]. Has seen [specific thing] firsthand.
[COMPANY] does: [What they do].
Their content angle: [2-3 themes they care about].
```

---

## Tips

- **The best posts come from opinions, not summaries.** What do you actually think about the article? Start there.
- **Near-miss > prediction.** "I've seen this fail three times" beats "this is the future of X."
- **Specific beats general.** "$47K shortfall on a Thursday afternoon" beats "cash flow challenges."
- **If you wouldn't say it out loud, don't write it.** Read the post aloud. Does it sound like you?

---

*Part of the [prompts](https://github.com/jforksy/prompts) collection.*
