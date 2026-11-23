# Hormozi skill for Claude Code (unofficial)

A [Claude Code skill](https://docs.claude.com/en/docs/claude-code/skills) that applies Alex Hormozi's business rules to your business. Use it for offers, pricing, lead generation, sales, retention, money models and scaling.

The skill first asks for your numbers. Then it finds your business stage and your one constraint, and gives one main recommendation with a source for each rule. It talks in his direct, numbers-first style, but it does not pretend to be him.

> **Unofficial.** This project is not made, approved or supported by Alex Hormozi or Acquisition.com. It contains paraphrased summaries with citations, not text from the books. Buy the books: they are better than this summary.

## Install

```bash
git clone https://github.com/wyatthamilton20-hash/hormozi-claude-skill
cp -r hormozi-claude-skill/hormozi ~/.claude/skills/
```

Then ask Claude Code something like "What would Hormozi do with my offer?" or "Hormozi this: I run a gym with 120 members and…".

## What it is built on

All content comes from the audiobooks that Hormozi published free on his podcast feed, *The Game with Alex Hormozi*, read by him:

| Source | Chapters used |
|---|---|
| *$100M Offers* | 7 parts (plus 1 duplicate upload of part 7) |
| *$100M Leads* | 11 parts |
| *$100M Money Models* | 9 parts, plus an 83-second launch trailer |
| *$100M Lost Chapters* | 26 chapters |
| Podcast Ep 895 and Ep 897 (about the *Money Models* launch) | 2 episodes. These were classed as book material by mistake. Some rules cite them. |

The skill does **not** use the rest of his podcast, his YouTube channel, guest appearances, courses or social media.

## How it was built

1. **Transcription.** Each chapter was downloaded from the public podcast RSS feed and transcribed on a local machine with Whisper (`whisper-large-v3-turbo`), with timestamps.
2. **Claim extraction.** One Claude Haiku agent per chapter wrote out every piece of advice as a structured claim: the rule, **when it applies** (business stage, revenue, bottleneck), numbers, the reason, the timestamp, and a short quote as proof.
3. **Verification.** A script checked each claim's quote against the transcript within 90 seconds of its timestamp. 1,682 of 1,740 claims passed (96.7%). The 58 that failed were removed.
4. **Synthesis.** One Claude Opus agent per topic grouped the claims into rules and built a decision tree by business stage. It also kept a log of rules that conflict between books (the newer book wins). Every advice line has a source tag `(id@HH:MM:SS)`. A script confirmed that all 1,220 tags exist in the verified claims, and that no long quote from a book was copied.
5. **Skill.** `SKILL.md` holds the diagnostic flow, the stage codes (S0–S5), the constraint codes (offer, leads, conversion, economics, retention, capacity) and the answer format. It loads only the reference file that a question needs.

## Files

```
hormozi/
  SKILL.md                    diagnostic flow, routing, voice and citation rules
  references/
    offers.md                 offer, pricing, positioning and niche
    money-models.md           attraction, upsell, downsell and continuity offers; CAC, LTV, payback
    lead-gen.md               core four, warm and cold outreach, referrals, affiliates
    sales.md                  sales process and closing
    paid-ads.md               paid advertising
    content-brand.md          free content and brand
    retention.md              churn and repeat purchase
    scaling.md                hiring, operations, founder focus
    mindset-wealth.md         mindset and wealth
```

Each reference file has: top rules, a decision tree by stage, a core rules table with conditions, frameworks, benchmarks, diagnostic questions, warnings, worked examples, a contradiction log, known gaps, and a sources table.

## How good is it? (honest result)

The skill was tested blind on **17 real hot-seat cases** from Hormozi's podcast. In each case, a business owner describes their business and Hormozi gives a verdict. The skill never saw these episodes. For each case, two blind judges compared the answer with what Hormozi actually told the owner. The same test ran on plain Claude Opus with no skill.

| | With this skill | Plain Claude Opus |
|---|---|---|
| Matched his main recommendation | 8 of 17 (47%) | 7 of 17 (41%) |
| Average score (0–2) | 1.00 | 1.12 |

**Result: the test failed.** The target was 80% and 15 points above plain Opus. The skill gives well-sourced, book-accurate advice, but it does not predict his live hot-seat calls better than Opus alone. The main cause: the skill often picks the wrong constraint, and in particular it calls too many problems "a lead problem". Live hot seats also cover topics the books do not, such as raising capital and acquisitions.

Use it as a structured, cited guide to the books, not as a copy of his judgment.

## Limits

- Books only (see the table above). Advice he gave elsewhere is not included.
- The default revenue bands for the stages (S0–S5) were chosen by the builder, not taken from the books.
- No legal, tax or investment advice.
