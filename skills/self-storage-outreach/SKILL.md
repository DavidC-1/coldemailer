---
name: self-storage-outreach
description: Campaign strategy and copy angles for cold outreach to independent self-storage facility owners and operators. Includes the community anchor personalization pattern (referencing a nearby staple restaurant to signal local knowledge), plus occupancy, reputation, and local competition angles. Run after /community-anchor-enrichment to unlock the hyper-local copy variants.
---

# Self-Storage Outreach

Campaign strategy for reaching independent self-storage owners — the people who own and operate 1–5 facilities and handle everything from pricing to plumbing. These are not corporate asset managers. They're community business owners.

## Who you're targeting

**Primary ICP:**
- Owner-operators of 1–3 self-storage facilities
- 50–500 units per facility
- Independent (not Public Storage, Extra Space, CubeSmart, Life Storage chain locations)
- In market for 3+ years (established business, not a new build)
- Decision maker = the owner, who is likely also the manager

**Title filters (for Prospeo / Blitz):**
- Owner, Co-Owner, Partner
- Operator, Facility Manager
- President, General Manager (at small companies only)

**Hard excludes:**
- National chains: Public Storage, Extra Space, CubeSmart, Life Storage, StorageMart, U-Haul (storage), Uncle Bob's
- Corporate REITs (these route to asset managers, not operators)
- New facilities (< 2 years old) — still filling occupancy, not yet profit-motivated on optimization

**How to identify independent:** company has one location on Google Maps OR website only mentions 1–3 facilities by name. National chains have 100+ locations listed.

## Their world (understand before writing copy)

Self-storage owners have 3 things they care about above all else:

1. **Occupancy** — Empty units = dead money. Industry average is 87–92%. Below 85% and they're stressed.
2. **Rate per unit** — They compete with 5–10 nearby facilities. Rate wars are common. Street rate vs. existing customer rate is a constant tension.
3. **Reputation** — Google is their #1 lead source. A 3.8-star facility loses to a 4.5-star competitor across the street every time.

Secondary concerns:
- Delinquent tenants and late payments (collections automation)
- Break-ins, vandalism, insurance claims
- After-hours access complaints
- Online booking vs. walk-in balance
- Climate-controlled unit demand going up

## Pain points to reference in copy

Use these when you don't have specific research data on the prospect:

- "Most storage owners I talk to say the same unit has been empty for 3+ months"
- "Google reviews tank whenever someone can't get in after hours"
- "Rate wars with the new Extra Space up the road"
- "Delinquency process is still paper letters and awkward phone calls"
- "Online booking is either a spreadsheet or a $400/month software they're not using"

---

## Campaign Angles

### Angle 1 — Community Anchor (Hyper-Local, Highest Response Rate)

**Requires:** `anchor_restaurant_name` and `anchor_restaurant_descriptor` from `/community-anchor-enrichment`

**Why it works:** Storage owners are local business owners. They identify with community institutions. Referencing a specific nearby restaurant proves you've looked at their specific location — not just their industry.

**Variables needed:**
- `{{anchor_restaurant_name}}` — e.g., "Millie's Diner"
- `{{anchor_restaurant_descriptor}}` — e.g., "breakfast spot"
- `{{anchor_city}}` — e.g., "Akron"
- `{{first_name}}`
- `{{company_name}}`

**Email 1 — Community anchor**

Subject options:
- `question for {{first_name}}`
- `{{anchor_city}} storage`
- `{{company_name}} and {{anchor_restaurant_name}}`

```
{{first_name}}, {{anchor_restaurant_name}} has been the go-to {{anchor_restaurant_descriptor}} in {{anchor_city}} for as long as {{company_name}} has been storing the neighborhood's stuff.

[Your one-sentence value prop — what you do and the outcome].

Worth a quick chat?
```

**Email 2 — Rotate to occupancy (threaded)**

```
{{first_name}}, the other thing I hear from storage owners in markets like {{anchor_city}} — the unit that's been empty 90+ days.

[What you do] cuts average vacancy time by [X] for operators like {{company_name}}.

Is that on your radar?
```

**Email 3 — New thread, reputation angle**

Subject: `Google reviews`

```
{{first_name}}, when someone in {{anchor_city}} searches "storage near me," your first impression is your Google rating.

[What you do] — [operators see X improvement in review count / rating in Y months].

Worth a look?
```

**Email 4 — Redirect or value bomb**

```
{{first_name}}, last note.

If pricing and occupancy aren't your current focus, let me know who handles [your product category] at {{company_name}} and I'll reach out to them instead.

Either way, appreciate your time.
```

---

### Angle 2 — Google Reviews / Reputation

**Requires:** Scrape the prospect's Google Maps page to pull their current star rating and count of negative reviews.

**Variables needed:**
- `{{google_rating}}` — e.g., "3.9"
- `{{negative_review_theme}}` — pulled from 1-star reviews, e.g., "after-hours access"
- `{{first_name}}`
- `{{company_name}}`

**Email 1**

Subject: `{{company_name}} reviews`

```
{{first_name}}, {{company_name}} is at {{google_rating}} stars on Google.

Saw a few reviews mentioning {{negative_review_theme}}. That pattern usually costs a facility 15–20% of new-tenant inquiries that never call.

[What you do about it in one sentence].

Is this something you're actively working on?
```

**How to get the data:** Use Google Maps API or manual lookup. Search `{{company_name}} {{city}} storage` on Google Maps, pull rating + most recent 1-star themes. With `/community-anchor-enrichment` data you already have the city. Enrich manually for the first 50 leads; automate with `/google-maps-list-builder` place details endpoint for larger lists.

---

### Angle 3 — Competitive Proximity

**Requires:** Know whether there's a new national chain (Extra Space, CubeSmart, etc.) within 1–2 miles of the target facility.

**Variables needed:**
- `{{nearby_competitor}}` — e.g., "Extra Space"
- `{{first_name}}`
- `{{company_name}}`

**Email 1**

Subject: `{{nearby_competitor}}`

```
{{first_name}}, there's a {{nearby_competitor}} within a mile of {{company_name}}.

From my experience, independent operators who survive that usually win on [local trust / customer service / price / specific thing you offer].

[What you do that helps independent operators compete].

Worth a look?
```

---

### Angle 4 — Occupancy / Vacancy Direct

**Best for:** Broad campaigns without enrichment data. Works as a fallback when `anchor_found=false`.

**Email 1**

Subject: `empty units`

```
{{first_name}}, most independent storage operators I talk to have at least 8–12% vacancy they can't move — units sitting empty despite being listed online.

[What you do] fills that gap for operators like {{company_name}} by [mechanism in one sentence].

Is occupancy still a priority or have you cracked it?
```

---

### Angle 5 — New Hire / Ownership Change

**Requires:** Evidence that a new owner or manager took over recently (new Google listing, ownership change record, county permit, LinkedIn).

**Email 1**

Subject: `question for {{first_name}}`

```
{{first_name}}, looks like you recently took over {{company_name}}.

Most new operators I talk to inherit the same three things: a rate structure that hasn't changed in 2 years, a Google rating that needs work, and at least one empty unit that's been sitting too long.

[What you do] helps with [one of those three].

Is any of that still on your plate?
```

---

## AI Personalization Variables (for `/personalization-subagent-pattern`)

When running the personalization sub-agent, provide this context per lead:

```json
{
  "lead_id": "...",
  "first_name": "...",
  "company_name": "...",
  "address": "...",
  "anchor_restaurant_name": "...",
  "anchor_restaurant_descriptor": "...",
  "anchor_city": "...",
  "google_rating": "...",
  "unit_count": "..."
}
```

**Prompt instructions for sub-agent:**

```
You are personalizing cold email fields for self-storage facility owners.

CONTEXT:
- We sell: [your one-sentence offer]
- ICP: Independent self-storage facility owners, 1-5 locations, owner-operator
- Offer: [your CTA]
- Tone: Peer-to-peer, direct, respectful. These are practical business owners, not tech executives.

FIELDS TO GENERATE (per lead):
- situation_line: Reference to their specific market using the anchor restaurant OR their rating/occupancy situation. 1 sentence. Do NOT fabricate specific metrics about their facility — only reference publicly visible data.
- value_line: Connect your product to their likely situation. Reference the community angle if anchor data is present. 1 sentence.
- cta_soft: Low-effort ask. "Worth a look?" / "Is this on your radar?" / "Worth a quick chat?" 1 sentence.

RULES:
1. If anchor_restaurant_name is present, use it in situation_line.
2. Never fabricate review counts, ratings, or occupancy numbers you don't have.
3. Never use "leverage", "synergy", "ecosystem", "innovative", "solutions".
4. No em dashes. Use periods or commas.
5. Max 20 words per field.
6. If lead data is thin, use the occupancy angle as default.
```

---

## List Building for Self-Storage

**Option A — Google Maps (fastest)**
```bash
npm run scrape -- --query="self storage" --state=OH --min-pop=5000 --output=./ohio-storage.csv
```
- Query: `"self storage"`, `"storage units"`, `"storage facility"`
- Filter out chains by name in `/icp-prompt-builder` qualification pass

**Option B — Prospeo (if you need named contacts)**
Self-storage owners are often not on LinkedIn. Prospeo title-first search:
- Title filters: `owner`, `operator`, `president`, `manager`
- Industry: real estate / storage / warehousing
- Company size: 1–10 employees

**Option C — Google Maps enriched with Blitz**
Google Maps → company domain → Blitz → owner email
Most reliable for small operators who don't have LinkedIn profiles.

**Chain exclusion list** (filter these from any list):
- Public Storage, Extra Space Storage, CubeSmart, Life Storage, StorageMart
- Simply Self Storage, National Storage Affiliates, SecureSpace, Merit Hill Capital
- U-Haul Storage, Budget Storage, Uncle Bob's Self-Storage, Compass Self Storage

---

## Sequence Timing

| Email | Day | Thread | Angle |
|-------|-----|--------|-------|
| 1 | 0 | New | Community anchor OR occupancy direct |
| 2 | 3–4 | Threaded | Rotate to occupancy / revenue |
| 3 | 7–8 | New | Google reputation |
| 4 | 11–12 | New or thread | Redirect or resource |

**Timezone note:** Storage owners are often on-site during business hours. Send emails 8–11am in THEIR timezone.

---

## QA Checklist

Before launching any self-storage campaign:

- [ ] Chain exclusion list applied (no Public Storage, Extra Space, etc.)
- [ ] `anchor_found=false` leads routed to static copy variant
- [ ] No fabricated metrics (don't claim to know their occupancy rate)
- [ ] `{{anchor_restaurant_name}}` is not a chain restaurant (spot-check 10)
- [ ] Tone check: peer-to-peer, not condescending ("most operators struggle with..." = OK; "you're clearly having trouble with..." = not OK)
- [ ] CTA is low-effort (replying takes 3–5 words)

---

## What to do next

1. **Build the list:** `/google-maps-list-builder` with `"self storage"` query, then `/icp-prompt-builder` to filter chains
2. **Find contacts:** `/blitz-list-builder` to get owner emails from facility domains
3. **Enrich:** `/community-anchor-enrichment` to add `{{anchor_restaurant_name}}` fields
4. **Write copy:** Use the Community Anchor angle above (Angle 1) as Email 1
5. **Personalize:** `/personalization-subagent-pattern` with the sub-agent prompt above
6. **Upload:** `/smartlead-campaign-upload-public` → draft → review → launch

## Related skills

- `/restaurant-list-builder` — builds the restaurant database for enrichment
- `/community-anchor-enrichment` — adds `{{anchor_restaurant_name}}` to each storage lead
- `/campaign-copywriting` — full stepwise copy creation (references this skill's angles)
- `/google-maps-list-builder` — builds the raw storage facility list
- `/blitz-list-builder` — finds owner contacts at each facility
- `/icp-prompt-builder` — qualification pass to filter chains before enrichment
