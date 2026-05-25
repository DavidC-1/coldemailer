# Campaign Strategy — Self-Storage CFE

**Source inputs:** [CFE-DEAL-IDEAS.md](../../CFE-DEAL-IDEAS.md), [CFE-LARGE-INDEPENDENTS.md](../../CFE-LARGE-INDEPENDENTS.md), [LEAD-MAGNET-BRAINSTORM.md](../../LEAD-MAGNET-BRAINSTORM.md)
**Core offer:** Operational lift for independent self-storage operators delivered through AI agents (voice/chat capture, ECRI, insurance attach, delinquency, document processing, AP), structured as consulting-for-equity — small fee + equity, scaling up at the mid-market into operating + option-to-acquire.
**Hard exclude:** Public Storage, Extra Space, CubeSmart, Life Storage, StorageMart, NSA, U-Haul Storage, Compass, Simply, SecureSpace, Merit Hill, Uncle Bob's.

---

## Customer Discovery (derived from source docs, not website crawl)

**Three distinct ICPs — do NOT run them in the same sequence.** Sender persona, magnet, and language differ for each.

**Important framing:** the N8N + Twilio + ElevenLabs stack is **PMS-agnostic** — any modern self-storage PMS with an API works (Tenant Inc/Nectar, Storable/storEDGE/SiteLink, syrasoft, Easy Storage Solutions, Hummingbird/Janus, Empower). Segment A is "any independent with a PMS-backed website," not "Nectar customers only."

**There is no live case study to cite in cold copy.** No "we run this for X" / "a 4-facility operator on Y" claims. Proof in this phase = the magnet itself (the worksheet they receive, the free trial of the actual work, the mystery-shop PDF). Third-party data (Radius+ 2026 benchmarks) and Frasier framing for Segment C carry the rest. Deal #12 (NOI Guarantee) is gated on 3 audited case studies and is further out than initially scoped.

| ICP | Profile | Speaks like | Lead magnet | Proof in the magnet |
|---|---|---|---|---|
| **A. PMS-equipped independent** | 2–10 facilities with any modern PMS (online unit availability + payment portal visible on site) | Operator who bought software but isn't using it | #4 Delinquency Leak Report | The worksheet itself — they send aging totals, they get back a recoverable-dollars model. Output IS the proof. |
| **B. Small independent** | 1–3 facilities, $300K–$1M gross, owner 55–75, often light/no PMS | Landlord, cash-and-cents | #1 5-Call Mystery Shop | The PDF showing how many of their own calls went unanswered. Their own data is the proof. |
| **C. Large independent** | 5–50 facilities, $2M–$30M EBITDA, family/regional | CEO, banker-savvy | #7 Dormant Asset Audit | 1-page memo on their actual portfolio + Radius+ 2026 / Frasier framework as third-party validation. |

**Lookalike anchors (Segment A):** operators with public signals of a modern PMS — online unit availability widget, embedded payment portal, "powered by" / "site by" footer credits (Tenant Inc, Storable, syrasoft, Easy Storage Solutions, Hummingbird). Source from each PMS vendor's customer-logos page.

**Lookalike anchors (Segment C):** regional 10–40 facility platforms with public CMBS debt (DDR), 2nd-gen family ownership, Storage Asset / Argus / Absolute as their current TPM (steal-able).

---

## Strategic Sequencing (start here)

**This quarter (Q2 2026):** Run Segment A only. You have the working integration and a real cost number ($40/mo software to run the stack). No case study yet — so the magnet has to carry the proof. Closing 2–3 PMS-equipped operators here generates the *first* case studies, which then unlock B and C copy.

**Once 2 deals close and produce documented outcomes:** Add Segment B (small independents) using the Mystery Shop magnet — by then your ElevenLabs voice stack should be live and the magnet *is* the demo (their own missed-call data).

**Once 3 audited results exist:** Add Segment C (large independents) with the Dormant Asset Audit. The QofE Pre-Read (#10) becomes the second-touch ask. Deal #12 (NOI Guarantee) is also unlocked at that point.

**Three Smartlead campaigns, three sender personas.** Don't try to be all three from the same inbox.

---

## Campaign Ideas (broadest → most niche)

### Segment A — PMS-equipped independents (any modern PMS)

| # | Campaign Name | Targeting Level | List Filters | AI Strategy | Value Proposition | Campaign Overview |
|---|---|---|---|---|---|---|
| A1 | **The $40/mo stack** | Broad | All independent operators with PMS-backed website (online unit availability widget + payment portal visible). Any PMS: Tenant Inc/Nectar, Storable/storEDGE/SiteLink, syrasoft, Easy Storage Solutions, Hummingbird, Empower. Chain-excluded. | None - static copy | Save money + make money: "the delinquency + abandonment + AI-call stack costs ~$40/mo in software to run against any modern PMS" | Subject: "$40/mo software." Body: 2 sentences describing the stack components (N8N + Twilio + ElevenLabs against PMS API) and the cost. NO case-study claim. Ask for 30+/60+/90+ aging totals so you can model their specific recoverable dollars. CTA: "reply with the numbers OR say 'send the template.'" The cost-shock + math-on-their-own-data carries the email. |
| A2 | **Delinquency Leak Report** (Magnet #4) | Focused | Same as A1 + replied with aging totals | LLM rewrite of aging totals → recoverable-dollars worksheet using industry benchmarks for 3-channel nudge sequence | Make money: convert paper-stuck delinquency dollars into recovered tenants | After they reply with aging totals, you send the 1-page worksheet. Output is a single recoverable-dollars number with breakdown by bucket. Their data + industry benchmarks ARE the proof — no third-party customer reference needed. Bridge to Deal #3 (Pure Performance, 50% of NOI lift, zero base fee) — this is the easiest closer without case studies because the operator only pays out of recovered dollars. |
| A3 | **Cart Abandonment Free Trial** (Magnet #5) | Focused | Operators with any PMS-backed storefront + abandonment notifications visible in public flow (storeyourway.com, storEDGE checkout, syrasoft, etc.) | None at email layer — the AI is in the trial delivery (live N8N + Twilio sequence) | Make money: "every abandonment notification you delete is a recoverable lead" | Day 10 pivot in the sequence. Offer: 30 days of cart-abandonment follow-up on your dime; they keep the recovered tenants. The trial IS the case study you don't have yet — every successful trial becomes a citable reference for the next campaign. Convert recovery numbers into paid engagement at day 30. |
| A4 | **Recent PMS Switchers** | Focused | Operators whose website footer/credits changed PMS in last 12 months (Wayback diff, PMS vendor case-study pages, LinkedIn announcements, BuiltWith change history) | Pull switch date + prior PMS if known | Save time: "you just switched PMS — here are the 3 things to automate first before the rollout fatigue sets in" | Targets the moment of maximum openness. Subject: "Your first 90 days on [PMS]." Body: name 3 workflows you've built (delinquency outreach, abandonment recovery, AI call handling). Offer one free for 30 days. CTA: "which one would help you the most?" |
| A5 | **Multi-Facility PMS Operators** (Niche) | Niche | 3+ facilities + any modern PMS + owner-operator (not REIT) + active on Google Maps | LinkedIn scrape for owner tenure + count of facilities visible on About page | Make money at portfolio scale: lift across all facilities, not one | Highest-value Segment A target. Subject: "[X] facilities on [their PMS]." Body: name the facility count, note that running the AI stack across multiple facilities compounds the savings. Position toward Deal #10 (Multi-Facility Platform Equity) without naming it. CTA: send portfolio-wide aging totals for the worksheet. |
| A6 | **PMS Vendor Customer-Logo Scrape** (Creative Stretch) | Niche | Companies appearing on Tenant Inc / Storable / syrasoft / Easy Storage Solutions customer logo pages or case-study sections | Static — vendor's own published customer list IS the filter | Save money + make money: PMS-validated operators already past the "do they have an API?" gate | Each PMS vendor publishes a customer logos page. That's a free, pre-qualified list of operators who (a) have an API-capable PMS and (b) are willing to be cited publicly (= less paranoid about tech). Scrape all of them, dedupe, run A1 copy variant against each. |

### Segment B — Small independents (1–3 facilities)

| # | Campaign Name | Targeting Level | List Filters | AI Strategy | Value Proposition | Campaign Overview |
|---|---|---|---|---|---|---|
| B1 | **Occupancy Direct** (no-AI baseline) | Broad | Google Maps "self storage" by state, chain-excluded, 3+ years in business | None — static copy from `self-storage-outreach` SKILL Angle 4 | Make money: fill the unit that's been empty 90+ days | Fallback campaign for any lead missing enrichment data. Subject: "empty units." Use the verbatim Angle 4 template from the self-storage-outreach skill. This is the always-on baseline; everything else is enriched on top. |
| B2 | **5-Call Mystery Shop** (Magnet #1) | Broad | Google Maps list of independents with a published main line | Automated dialer logs answer/voicemail/hold-time → templated 1-page PDF; subject + body name the specific facility | Make money: "5 calls to [Facility Name], 1-page report back" | The single best demonstration of your voice-agent capability — your voice agents deliver the magnet itself, making the magnet the demo. Day 0 subject: "5 calls to [Facility Name]." Body: 2 sentences, no links, no pitch. Day 3: nudge. Captured-call rate < 95% is the bridge to Deal #2 (Front Door). |
| B3 | **Community Anchor + Mystery Shop** (Niche) | Niche | Self-storage skill Angle 1 + magnet #1 — only leads with `anchor_found=true` from `/community-anchor-enrichment` | Anchor restaurant + mystery shop scheduled | Make money + signal "I know your neighborhood" | Strongest Segment B email. Subject: "[Anchor Restaurant Name] and [Facility Name]." Body 1: anchor reference (Angle 1 template). Body 2 (day 3): "ran 5 calls this week, here's what I found." Then send the PDF. Two-step magnet — anchor is the door-opener, shop is the value. |
| B4 | **REIT Pricing Map** (Magnet #2) | Focused | Independents within 5 miles of any Extra Space / Public Storage / CubeSmart / Life Storage | Daily scrape of REIT web rates for 10x10 climate + non-climate at every REIT facility nearby; 1-page map with spread + velocity | Make money: see what the REITs are charging in real time | Day 8 pivot in the Segment B sequence. Subject: "Extra Space rates within 5mi of [City]." Body: offer the map. Use the REIT scraper you'll build for Deal #1's dynamic pricing rules engine — magnet doubles as product proof. |
| B5 | **NOI Leak Estimator** (Magnet #3) | Focused | Independents who replied positively to mystery shop or pricing map but didn't book | 5-question reply-by-email survey → templated leak model (capture × LTV + insurance gap + ECRI gap) | Make money: "here's the annualized leak on your facility" — 3 line items + total | Third-touch magnet for warm-but-not-closed Segment B leads. Output sets up Deal #1 / #3 pitches with operator-validated baseline numbers. CTA: "ready to plug those leaks?" Subject: "3 numbers from your facility." |
| B6 | **GBP & Review-Response Audit** (Magnet #6) | Focused | Independents with Google Business Profile response rate < 50% in last 90 days, or rating < 4.0 | Public scrape: response rate, response time, photo cadence, NAP consistency, hours accuracy. Score 0–100 vs. top facility in their county | Mitigate risk + make money: GBP rot is killing your inbound | Lower-value magnet, use only when inbox objections are "I already have a website guy." Subject: "[Facility Name] vs [Top Local Competitor] on Google." Score the gap. |
| B7 | **New Hire / Ownership Change** (REQUIRED CAMPAIGN) | Focused | Detected ownership change <12 months (Google listing change, county permits, LinkedIn role change, Wayback diff on About page) | Pull ownership-change date + prior listing | Save time: "new operators inherit the same 3 things — rate structure 2 years stale, GBP rot, one unit empty too long" | Subject: "question for {{first_name}}." Body: Angle 5 from self-storage-outreach skill, but escalate to offer a free 30-day operational diagnostic. New owners are openness-maximum. |
| B8 | **Manager Just Quit / Retiring** (Creative Stretch) | Niche | Indeed/LinkedIn job postings for "self storage manager" or "facility manager" within last 60 days at independent operators | Job posting parse → reference the open role | Save money + save time: "your manager just left — replace 70% of what they did with AI" | Direct pitch into Deal #11 (Labor Reduction Equity). Subject: "saw you're hiring a manager at [Facility]." Body: "before you hire, here's what AI can do for 70% of the role at a fraction of the cost." Bridge to Deal #11 fee structure (50% of labor savings). |

### Segment C — Large independents (5–50 facilities)

| # | Campaign Name | Targeting Level | List Filters | AI Strategy | Value Proposition | Campaign Overview |
|---|---|---|---|---|---|---|
| C1 | **Dormant Asset Audit** (Magnet #7, REQUIRED LOOKALIKE) | Niche | Named regional operators (10–40 facilities), 2nd-gen family preferred, identified via state corporate registries + Radius+ operator lists | 60-min desk research per target: county parcel data (unbuilt acreage), customer list size estimate, brand licensing scan, CMBS lease maturity, OpCo/PropCo registry check | Make money + exit positioning: "3 assets you may be sitting on that aren't being valued" | Lowest-volume / highest-deal-value campaign. ~20–50 named targets, not 5000. Subject: "1-page memo on [Company Name]." Body: name 2 of 3 dormant assets in the teaser. No call ask. The memo IS the qualification. Bridges into Deal #6 (JV), Deal #10 (Platform Equity), or Deal #15 (Acquisition Option). |
| C2 | **CMBS Maturity Pressure Test** (Magnet #8) | Niche | Operators with CMBS debt maturing in next 24 months (Trepp / DDR public data + Radius+ debt scrape) | Pull current debt service + project at today's rate; compute DSCR delta + required NOI lift | Mitigate risk + make money: "your [lender] facility maturing [date] reprices [X%] → [Y%]" | Second-touch for Segment C non-responders, OR first-touch when a debt signal is identified. Subject: "Your [bank] maturity." Body: 3-sentence DSCR math. Direct bridge to Deal #9 (Refi Equity Trade). |
| C3 | **Bid-Ask Bridge Worksheet** (Magnet #9) | Focused | Family-owned regionals + known sale interest signals (advisor on retainer, broker engagement, public M&A interest) | None — static reusable worksheet | Make money: "your target price / today's market multiple = gap; here's the NOI lift required over 24 months to close it" | Lower-effort secondary magnet. Subject: "1-page bid-ask worksheet." Use as fallback when Dormant Asset isn't sticking. References Radius+ 2026 data + Live Oak Bank / Mazur quotes as third-party proof. |
| C4 | **QofE Pre-Read** (Magnet #10, SECOND-TOUCH ONLY) | Niche | Segment C leads who already engaged positively with C1, C2, or C3 + signed NDA | LLM-assisted read of 12-month P&L: standard EBITDA add-backs, ECRI gaps, insurance attach gaps, below-market wages → 1-page memo with normalized add-backs × cap rate = EV uplift | Make money on exit: "we identified $X of add-backs lifting EV by $Y at 7% cap" | Highest-trust magnet on the list. They send the P&L PDF under NDA. Never offer cold. Becomes the literal first step of the CFE "Step 1 Diagnostic Engagement" from CFE-LARGE-INDEPENDENTS.md §3. Converts into $50K diagnostic fee or directly into F2E engagement. |
| C5 | **Family-Owned Succession Watch** (Creative Stretch) | Niche | 2nd-gen family operators + owner age 60+ (LinkedIn/news scrape) + no clear internal successor (LinkedIn family-name match in management) | LinkedIn + obit/news scrape for ownership generation signals | Save time + exit: "ride the recovery, hand off operations, retire on the upside" | Direct pitch into Deal #7 (Succession Equity Bridge). Subject: "succession at [Company]." Body: 3 sentences acknowledging the family-business reality + the alternative to selling in a trough. CTA: "ready to talk about what a 5-year handoff could look like?" |
| C6 | **TPM Displacement** (Creative Stretch) | Niche | Operators currently with Storage Asset, Argus, Absolute, or Merit Hill as TPM (LinkedIn employer scrape + corporate filings) | Identify current TPM + tenure | Save money: "Argus charges 6% — I charge 4% + take the other 2% in equity" | Direct pitch into Deal #8 (Third-Party Management Plus). Subject: "[Their TPM] vs aligned ops." Body: math on 4% cash + 2% equity vs straight 6%. CTA: "happy to send the side-by-side." |
| C7 | **Distressed Lease-Up Rescue** (Creative Stretch) | Niche | 2022–2024 deliveries with occupancy < 60% (Radius+ supply chapter list + Google Maps recency check) | County permit scrape for build year + Maps review count as occupancy proxy | Mitigate risk: "stalled lease-up → forced sale" alternative | Direct pitch into Deal #5 (Distressed Lease-Up Rescue). Subject: "[Facility Name] occupancy." Body: 3 sentences acknowledging the lease-up reality without judgment, offer rescue partnership. CTA: "want to see what a recovery partnership looks like?" |

### Cross-segment

| # | Campaign Name | Targeting Level | List Filters | AI Strategy | Value Proposition | Campaign Overview |
|---|---|---|---|---|---|---|
| X1 | **Creative Ideas / 3-Use-Case** (REQUIRED) | Broad | Any independent with a public website | Crawl prospect's site → generate 3 specific automation use cases (voice/chat/ECRI/insurance/delinquency) keyed to what their site reveals about their setup | Make money + save time, custom per facility | "I had an idea for [Facility Name]" + 3 bullet points specific to their site (e.g., "your website has a quote form but no after-hours fallback — voice agent picks that up at midnight"). Lowest-friction broad fallback when no segment-specific signal lands. |
| X2 | **AI-Adoption Skeptic Reversal** (Creative Stretch, No-AI) | Focused | Storage owners visible on Self-Storage Talk / SSA forums who've posted skeptical comments about AI/chatbots in last 12 months | None — static reframe | Mitigate risk: "we don't try to close — we capture and hand to you in 60 seconds" | Direct response to the skeptic quote pattern in CFE-DEAL-IDEAS.md (ai-technology-adoption.md reference). Subject: "not a chatbot." Body: reframe — hold-and-hand-off pattern, never a closer. Converts skeptics specifically. |

---

## No-AI Campaigns

**N1. "The $40/mo stack" (A1)** — pure static copy. Subject line IS the offer. Works because the segment filter (PMS-equipped independents) is tight and the cost number is concrete. Reply rate driven by cost shock + the ask for *their* data (not a claim about someone else's).

**N2. "Occupancy Direct" (B1)** — verbatim Angle 4 from the self-storage-outreach skill. Always-on baseline for any small-independent lead missing enrichment data. Don't pause it; let it run as your floor.

**N3. "AI-Adoption Skeptic Reversal" (X2)** — works because the entire value is in the reframe ("not a chatbot"), and the audience is self-identified by forum activity. Static body, dynamic list source.

---

## Front-End Offer Suggestions (the magnets)

You already have a 10-magnet menu in LEAD-MAGNET-BRAINSTORM.md. The mapping above places each magnet behind the right segment's campaign:

1. **Segment A primary:** Magnet #4 (Delinquency Leak Report) — 20/20 rubric, fastest path to first close
2. **Segment A secondary:** Magnet #5 (Cart Abandonment Free Trial) — strongest close, harder delivery
3. **Segment B primary:** Magnet #1 (5-Call Mystery Shop) — magnet IS the voice-agent demo
4. **Segment B secondary:** Magnet #2 (REIT Pricing Map), then #3 (NOI Leak Estimator), then #6 (GBP Audit)
5. **Segment C primary:** Magnet #7 (Dormant Asset Audit) — only viable cold first-touch at this level
6. **Segment C secondary:** Magnet #8 (Refi Pressure Test), #9 (Bid-Ask Worksheet)
7. **Segment C late-stage only:** Magnet #10 (QofE Pre-Read) — second touch after positive engagement

---

## Build Order (what to ship this week)

In priority order, restated from LEAD-MAGNET-BRAINSTORM.md §"What to build first":

1. **Delinquency Leak worksheet template** (1 page, takes aging totals → recoverable dollars) — unblocks Segment A entirely. Already running for Jyoti, just productize the diagnostic version.
2. **Nectar / Tenant Inc operator list** — WHOIS / subdomain enum on storeyourway.com, Tenant Inc partner logos page scrape, "powered by Nectar" footer scrape via Common Outbound Lists or BuiltWith. Filter to 2+ facilities.
3. **Mystery Shop pipeline** — phone list → automated dialer (Twilio for first 50, ElevenLabs once live) → call log → templated 1-page PDF. Unblocks Segment B.
4. **REIT rate scraper** — daily pull of 10x10 climate + non-climate rates from Extra Space / Public Storage / CubeSmart / Life Storage by zip. Becomes both magnet #2 AND the dynamic pricing rules engine you sell in Deal #1.
5. **Dormant Asset desk-research template** — 60-min checklist (county parcels, brand licensing, OpCo/PropCo registry, CMBS DDR pull). Don't build until Segment A has 2 closed deals as proof.

---

## Sender Personas (3 separate Smartlead campaigns)

- **Segment A sender:** "Automation builder" voice. Reference Jyoti case study directly (pending permission). Mailbox name: e.g., david@storage-stack.com or similar build-focused domain.
- **Segment B sender:** "Founder / operator-partner" voice. Anchor on community + cash. Mailbox name: warmer, partnership-sounding.
- **Segment C sender:** "Advisor / dealmaker" voice. Reference Radius+ data + Frasier-style framing. Mailbox name: advisor-sounding (firm name preferred over individual).

Do NOT cross-pollinate. Different sequences, different threading, different proof points.

---

## Open Questions Before Launch

1. **No case study yet** — every campaign here is built assuming proof comes from the magnet itself, not testimonials. The first 1–2 closes in Segment A are the priority because they unlock named references for everything downstream. Don't slow down to acquire case studies first — get the magnet in market and let the closes generate the case studies.
2. **Mystery shop recording legality per state** — log human-observable signals only (answered / voicemail / hold time) until you have a per-state legal review for audio recording in two-party-consent states.
3. **Capital backing for Segment C** — Deals #5, #9 imply capital. Confirm your first-loss budget before pitching Magnet #8 (Refi Pressure Test).
4. **Equity language gating** — never in first touch. CFE / option / equity language goes in follow-up #3 onward, after a positive reply.

---

## What to do next

**Pick one campaign and run `/campaign-copywriting`.** Recommend starting with **A1 (The $40/mo stack)** because it's static copy, fastest to ship, and validates whether PMS-equipped operators reply to the cost-shock + their-own-data ask. Then **A2 (Delinquency Leak Report)** as the natural second touch — the worksheet is the proof.

The first closed deal from A1/A2 becomes your first citable reference, which then strengthens **B3 (Community Anchor + Mystery Shop)** and later **C1 (Dormant Asset Audit)**.

Hold Segment C until 3 audited results exist. Deal #12 (NOI Guarantee) is gated on the same.
