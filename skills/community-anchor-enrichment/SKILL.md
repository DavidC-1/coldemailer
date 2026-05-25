---
name: community-anchor-enrichment
description: Enriches a local business leads list with a nearby "community anchor" — the closest staple community restaurant to each lead's address. Outputs {{anchor_restaurant_name}}, {{anchor_restaurant_descriptor}}, {{anchor_city}} fields for hyper-local personalization. Best used for self-storage, HVAC, roofing, insurance, or any local business outreach where knowing their neighborhood builds credibility.
---

# Community Anchor Enrichment

Adds one field to your leads that most cold emailers can't fake: **the name of a real local institution near each prospect's business.**

Referencing a community staple restaurant in outreach copy signals to a local business owner that you actually know their market — not just their zip code. You're not blasting 10,000 storage owners. You know the diner where they probably eat breakfast.

## What this produces

For each lead in your CSV, it adds:

| Field | Example | Used in copy as |
|-------|---------|-----------------|
| `anchor_restaurant_name` | Joe's Diner | `{{anchor_restaurant_name}}` |
| `anchor_restaurant_descriptor` | breakfast spot | `{{anchor_restaurant_descriptor}}` |
| `anchor_restaurant_rating` | 4.6 | — (QA use) |
| `anchor_restaurant_reviews` | 847 | — (QA use) |
| `anchor_restaurant_address` | 123 Main St | — (QA use) |
| `anchor_city` | Springfield | `{{anchor_city}}` |
| `anchor_distance_miles` | 0.4 | — (QA use) |
| `anchor_found` | true / false | — (skip leads where false) |

## How it fits in the cold email flow

```
/restaurant-list-builder → scored restaurants.csv
                                  ↓
your leads.csv (storage facilities with addresses)
                                  ↓
/community-anchor-enrichment → enriched-leads.csv
                                  ↓
/campaign-copywriting (use {{anchor_restaurant_name}} in copy)
                                  ↓
/personalization-subagent-pattern (scale it per lead)
```

## What You Need

1. **Node.js 18+** and **npm**
2. **RapidAPI key** with Maps Data API subscription
3. A **leads CSV** with at minimum: `company_name`, `address` (or `lat` + `lng`)
4. A **restaurants CSV** from `/restaurant-list-builder` — precomputed for the same market

The restaurants CSV can be reused across multiple campaigns in the same metro. You don't need to re-scrape it each time.

```bash
export RAPIDAPI_KEY=your_key_here
```

## Project Setup

```bash
mkdir community-anchor-enrichment && cd community-anchor-enrichment
npm init -y
npm install typescript bottleneck csv-parse
npm install -D @types/node tsx
```

`package.json` scripts:
```json
{
  "scripts": {
    "enrich": "tsx src/index.ts"
  }
}
```

`tsconfig.json`:
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "esModuleInterop": true,
    "strict": true,
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

## File Structure

```
community-anchor-enrichment/
  src/
    index.ts        # CLI entry + orchestrator
    client.ts       # Google Maps client (copy from /google-maps-list-builder)
    geo.ts          # Haversine distance + geocoding
    match.ts        # Restaurant matching logic
    csv.ts          # CSV read/write
    types.ts        # Types
```

## Core Files

### src/types.ts

```typescript
export interface Lead {
  [key: string]: string;
  company_name: string;
  address: string;
}

export interface ScoredRestaurant {
  place_id: string;
  name: string;
  address: string;
  lat: number;
  lng: number;
  rating: number;
  reviews_count: number;
  is_chain: string;
  community_score: number;
  qualifies: string;
  category?: string;
}

export interface AnchorResult {
  anchor_restaurant_name: string;
  anchor_restaurant_descriptor: string;
  anchor_restaurant_rating: string;
  anchor_restaurant_reviews: string;
  anchor_restaurant_address: string;
  anchor_city: string;
  anchor_distance_miles: string;
  anchor_found: string;
}
```

### src/geo.ts

Haversine distance between two lat/lng pairs, plus address-to-coordinates geocoding via the Maps Data API.

```typescript
import type { GoogleMapsClient } from './client.js';

export function haversineDistanceMiles(
  lat1: number, lng1: number,
  lat2: number, lng2: number
): number {
  const R = 3958.8; // Earth radius in miles
  const dLat = ((lat2 - lat1) * Math.PI) / 180;
  const dLng = ((lng2 - lng1) * Math.PI) / 180;
  const a =
    Math.sin(dLat / 2) ** 2 +
    Math.cos((lat1 * Math.PI) / 180) *
    Math.cos((lat2 * Math.PI) / 180) *
    Math.sin(dLng / 2) ** 2;
  return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
}

export async function geocodeAddress(
  client: GoogleMapsClient,
  address: string
): Promise<{ lat: number; lng: number } | null> {
  try {
    return await client.geocode(address);
  } catch {
    return null;
  }
}

export function extractCity(address: string): string {
  // "123 Main St, Springfield, IL 62701" → "Springfield"
  const parts = address.split(',');
  if (parts.length >= 2) {
    const cityState = parts[parts.length - 2].trim();
    // Remove state abbreviation if present: "Springfield IL" → "Springfield"
    return cityState.replace(/\s+[A-Z]{2}$/, '').trim();
  }
  return '';
}
```

### src/match.ts

Finds the best community anchor restaurant for a given lat/lng.

```typescript
import { haversineDistanceMiles } from './geo.js';
import type { ScoredRestaurant, AnchorResult } from './types.js';

const DESCRIPTOR_MAP: Record<string, string> = {
  diner: 'diner',
  breakfast: 'breakfast spot',
  brunch: 'brunch spot',
  italian: 'Italian restaurant',
  mexican: 'Mexican restaurant',
  bbq: 'BBQ joint',
  barbecue: 'BBQ joint',
  steakhouse: 'steakhouse',
  seafood: 'seafood spot',
  pizza: 'pizza place',
  burger: 'burger joint',
  sushi: 'sushi restaurant',
  thai: 'Thai restaurant',
  chinese: 'Chinese restaurant',
  greek: 'Greek restaurant',
  cafe: 'café',
  coffee: 'coffee shop',
};

function guessDescriptor(restaurant: ScoredRestaurant): string {
  const nameLower = (restaurant.name + ' ' + (restaurant.category ?? '')).toLowerCase();
  for (const [keyword, descriptor] of Object.entries(DESCRIPTOR_MAP)) {
    if (nameLower.includes(keyword)) return descriptor;
  }
  return 'local restaurant';
}

export interface MatchOptions {
  maxDistanceMiles: number;
  minCommunityScore: number;
}

export function findBestAnchor(
  leadLat: number,
  leadLng: number,
  restaurants: ScoredRestaurant[],
  opts: MatchOptions = { maxDistanceMiles: 2.0, minCommunityScore: 15 }
): AnchorResult {
  const empty: AnchorResult = {
    anchor_restaurant_name: '',
    anchor_restaurant_descriptor: '',
    anchor_restaurant_rating: '',
    anchor_restaurant_reviews: '',
    anchor_restaurant_address: '',
    anchor_city: '',
    anchor_distance_miles: '',
    anchor_found: 'false',
  };

  // Only consider qualified community restaurants
  const candidates = restaurants
    .filter(r => r.qualifies === 'true' && r.is_chain === 'false')
    .filter(r => Number(r.community_score) >= opts.minCommunityScore)
    .map(r => ({
      ...r,
      distance: haversineDistanceMiles(leadLat, leadLng, r.lat, r.lng),
    }))
    .filter(r => r.distance <= opts.maxDistanceMiles)
    // Sort by community score descending (prefer the most established, not just closest)
    .sort((a, b) => Number(b.community_score) - Number(a.community_score));

  if (candidates.length === 0) return empty;

  const best = candidates[0];
  const city = best.address.split(',').slice(-2, -1)[0]?.trim().replace(/\s+[A-Z]{2}$/, '').trim() ?? '';

  return {
    anchor_restaurant_name: best.name,
    anchor_restaurant_descriptor: guessDescriptor(best),
    anchor_restaurant_rating: String(best.rating),
    anchor_restaurant_reviews: String(best.reviews_count),
    anchor_restaurant_address: best.address,
    anchor_city: city,
    anchor_distance_miles: best.distance.toFixed(2),
    anchor_found: 'true',
  };
}
```

### src/csv.ts

```typescript
import { readFile, writeFile, mkdir } from 'fs/promises';
import { dirname } from 'path';
import type { Lead, ScoredRestaurant } from './types.js';

function parseCSV(raw: string): Record<string, string>[] {
  const lines = raw.trim().split('\n');
  const headers = lines[0].split(',').map(h => h.replace(/^"|"$/g, '').trim());
  return lines.slice(1).map(line => {
    const values: string[] = [];
    let current = '';
    let inQuotes = false;
    for (const ch of line) {
      if (ch === '"') { inQuotes = !inQuotes; continue; }
      if (ch === ',' && !inQuotes) { values.push(current); current = ''; continue; }
      current += ch;
    }
    values.push(current);
    return Object.fromEntries(headers.map((h, i) => [h, values[i] ?? '']));
  });
}

export async function readLeads(path: string): Promise<Lead[]> {
  const raw = await readFile(path, 'utf-8');
  return parseCSV(raw) as Lead[];
}

export async function readRestaurants(path: string): Promise<ScoredRestaurant[]> {
  const raw = await readFile(path, 'utf-8');
  return parseCSV(raw) as ScoredRestaurant[];
}

function escape(val: string | undefined | null): string {
  if (val == null) return '';
  return val.includes(',') || val.includes('"') || val.includes('\n')
    ? `"${val.replace(/"/g, '""')}"`
    : val;
}

export async function writeEnrichedCSV(
  rows: Record<string, string>[],
  outputPath: string
): Promise<void> {
  if (rows.length === 0) return;
  await mkdir(dirname(outputPath), { recursive: true });
  const headers = Object.keys(rows[0]);
  const lines = [
    headers.join(','),
    ...rows.map(r => headers.map(h => escape(r[h])).join(',')),
  ];
  await writeFile(outputPath, lines.join('\n') + '\n', 'utf-8');
}
```

### src/index.ts

```typescript
import { GoogleMapsClient } from './client.js';
import { readLeads, readRestaurants, writeEnrichedCSV } from './csv.js';
import { geocodeAddress, extractCity } from './geo.js';
import { findBestAnchor } from './match.js';

function getArg(args: string[], prefix: string): string | undefined {
  const match = args.find(a => a.startsWith(prefix));
  return match ? match.split('=').slice(1).join('=') : undefined;
}

async function main() {
  const args = process.argv.slice(2);

  const leadsPath = getArg(args, '--leads=');
  const restaurantsPath = getArg(args, '--restaurants=');
  const output = getArg(args, '--output=') || './output/enriched-leads.csv';
  const maxMiles = parseFloat(getArg(args, '--max-miles=') || '2.0');
  const minScore = parseFloat(getArg(args, '--min-score=') || '15');

  if (!leadsPath || !restaurantsPath) {
    console.log(`
Community Anchor Enrichment

Adds nearby community restaurant to each lead for hyper-local personalization.

USAGE:
  npm run enrich -- --leads=./data/storage-facilities.csv --restaurants=./data/restaurants.csv

OPTIONS:
  --leads=PATH          CSV of leads with "address" column (required)
  --restaurants=PATH    CSV from /restaurant-list-builder (required)
  --output=PATH         Output CSV path (default ./output/enriched-leads.csv)
  --max-miles=N         Max distance to match a restaurant (default 2.0)
  --min-score=N         Min community score to qualify (default 15)

ADDS COLUMNS:
  anchor_restaurant_name     → use as {{anchor_restaurant_name}} in copy
  anchor_restaurant_descriptor  → "diner", "breakfast spot", "Italian restaurant"
  anchor_city                → city name extracted from restaurant address
  anchor_distance_miles      → how close the match is (QA)
  anchor_found               → true/false (filter out false before upload)

ENVIRONMENT:
  RAPIDAPI_KEY          Required for geocoding addresses
`);
    process.exit(1);
  }

  const apiKey = process.env.RAPIDAPI_KEY;
  if (!apiKey) {
    console.error('Error: RAPIDAPI_KEY environment variable is required');
    process.exit(1);
  }

  const client = new GoogleMapsClient({ apiKey, requestsPerSecond: 2 });
  const leads = await readLeads(leadsPath);
  const restaurants = await readRestaurants(restaurantsPath);
  const qualifiedRestaurants = restaurants.filter(r => r.qualifies === 'true');

  console.log(`Leads: ${leads.length}, Community restaurants available: ${qualifiedRestaurants.length}`);
  console.log(`Search radius: ${maxMiles} miles, min community score: ${minScore}\n`);

  const enriched: Record<string, string>[] = [];
  let found = 0;
  let geocodeFailed = 0;

  for (let i = 0; i < leads.length; i++) {
    const lead = leads[i];
    const address = lead['address'] || lead['Address'] || '';

    process.stdout.write(`  [${i + 1}/${leads.length}] ${lead['company_name'] || address}... `);

    if (!address) {
      enriched.push({ ...lead, anchor_found: 'false', anchor_restaurant_name: '', anchor_restaurant_descriptor: '', anchor_city: '', anchor_distance_miles: '', anchor_restaurant_rating: '', anchor_restaurant_reviews: '', anchor_restaurant_address: '' });
      console.log('no address');
      continue;
    }

    // Use existing lat/lng if available, otherwise geocode
    let lat = parseFloat(lead['lat'] || '');
    let lng = parseFloat(lead['lng'] || '');

    if (!lat || !lng) {
      const coords = await geocodeAddress(client, address);
      if (!coords) {
        geocodeFailed++;
        enriched.push({ ...lead, anchor_found: 'false', anchor_restaurant_name: '', anchor_restaurant_descriptor: '', anchor_city: extractCity(address), anchor_distance_miles: '', anchor_restaurant_rating: '', anchor_restaurant_reviews: '', anchor_restaurant_address: '' });
        console.log('geocode failed');
        continue;
      }
      lat = coords.lat;
      lng = coords.lng;
    }

    const anchor = findBestAnchor(lat, lng, qualifiedRestaurants, {
      maxDistanceMiles: maxMiles,
      minCommunityScore: minScore,
    });

    if (anchor.anchor_found === 'true') {
      found++;
      console.log(`✓ ${anchor.anchor_restaurant_name} (${anchor.anchor_distance_miles}mi)`);
    } else {
      console.log('no nearby anchor found');
    }

    enriched.push({ ...lead, lat: String(lat), lng: String(lng), ...anchor });
  }

  await writeEnrichedCSV(enriched, output);

  console.log(`\nResults:`);
  console.log(`  Enriched with anchor: ${found}/${leads.length} (${Math.round(found / leads.length * 100)}%)`);
  console.log(`  Geocode failures: ${geocodeFailed}`);
  console.log(`  Saved to: ${output}`);
  console.log(`\nTip: Filter to anchor_found=true before uploading to Smartlead.`);
  console.log(`     Use anchor_found=false leads for a static-copy variant.`);
}

main().catch(err => {
  console.error('Fatal:', err.message);
  process.exit(1);
});
```

Copy `src/client.ts` from `/google-maps-list-builder` — identical.

## Running It

```bash
# Basic run
npm run enrich -- \
  --leads=./data/ohio-storage-facilities.csv \
  --restaurants=./data/ohio-restaurants.csv

# Tighter radius for dense urban markets
npm run enrich -- \
  --leads=./data/chicago-storage.csv \
  --restaurants=./data/chicago-restaurants.csv \
  --max-miles=1.0

# Wider radius for rural markets, lower score threshold for smaller towns
npm run enrich -- \
  --leads=./data/rural-storage.csv \
  --restaurants=./data/rural-restaurants.csv \
  --max-miles=5.0 \
  --min-score=10
```

## How to use the enrichment in copy

After enrichment, each lead that has `anchor_found=true` gets two copy treatment paths:

**Path A — Community anchor variant (hyper-local)**
```
{{first_name}}, {{anchor_restaurant_name}} has been the go-to {{anchor_restaurant_descriptor}}
in {{anchor_city}} for decades.

Your storage facility has been serving the same community just as long.

[Value prop for why you're reaching out]

Worth a quick chat?
```

**Path B — Static copy (for leads where anchor_found=false)**
Standard campaign copy without the local reference. Upload as a separate Smartlead variant.

## QA before uploading

Spot-check 10 random enriched leads:
- Is `anchor_restaurant_name` a real local restaurant (not a chain)?
- Is `anchor_distance_miles` reasonable (should be < 2.0 for most urban leads)?
- Does `anchor_city` match the lead's market?

Reject any lead where `anchor_restaurant_name` contains a chain name — it means the chain filter in `/restaurant-list-builder` missed it. Add that chain to the `KNOWN_CHAINS` list and re-run.

## Coverage expectations

| Market type | Expected anchor_found rate |
|-------------|---------------------------|
| Dense urban (NYC, Chicago, LA) | 85–95% |
| Mid-size city (Columbus, Memphis) | 70–85% |
| Suburban / small city | 55–75% |
| Rural | 30–55% |

For markets with low coverage, widen `--max-miles` or lower `--min-reviews` threshold in the restaurant scrape.

## What to do next

Take the `enriched-leads.csv` and run `/self-storage-outreach` to write copy that incorporates `{{anchor_restaurant_name}}`. Then run `/personalization-subagent-pattern` to generate the `situation_line` and `value_line` per lead using the anchor data.

## Related skills

- `/restaurant-list-builder` — produces the restaurants CSV this skill consumes
- `/self-storage-outreach` — campaign angles that use the anchor fields
- `/personalization-subagent-pattern` — scales the anchor personalization to full list
- `/smartlead-campaign-upload-public` — takes the final enriched leads + variants.yaml to launch
