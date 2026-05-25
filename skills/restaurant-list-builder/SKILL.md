---
name: restaurant-list-builder
description: Build targeted lists of staple community restaurants — independent, high-review, long-established. Filters out chains automatically. Output is a scored CSV ready for direct outreach OR as input to /community-anchor-enrichment to personalize outreach to nearby businesses (e.g., self-storage, HVAC, insurance).
---

# Restaurant List Builder

Scrapes Google Maps for restaurants and scores them on "community staple" criteria: independent (not a chain), high review count, strong rating. The output is a CSV of businesses that have proven themselves in a local market over time.

## Two use cases

**Use case A — Direct outreach to restaurant owners**
Run this skill, pipe results into `/blitz-list-builder` to find owner contacts, then run `/self-storage-outreach` (or your own campaign skill) to write copy.

**Use case B — Local community enrichment signal**
If you're selling to self-storage, HVAC, roofing, insurance, or any local business, run this skill in a market and then run `/community-anchor-enrichment` to attach a nearby "community anchor restaurant" to each lead. This gives your copy a hyper-local reference point — proof you know their specific neighborhood.

## What makes a "community staple"

| Signal | Threshold | Why |
|--------|-----------|-----|
| Rating | ≥ 4.0 stars | Survived local competition on quality |
| Review count | ≥ 200 reviews | Years of repeat customers, not a new spot |
| Chain status | `is_chain = false` | Independent = owner is a person you can reach |
| Category | restaurant / diner / café / etc. | Not a bar, food truck ghost kitchen, etc. |

A community score is computed: `rating × ln(reviews_count)`. Restaurants above 20 are typically well-established local favorites.

## What You Need

1. **Node.js 18+** and **npm**
2. **RapidAPI key** with Maps Data API subscription (same key as `/google-maps-list-builder`)

```bash
export RAPIDAPI_KEY=your_key_here
```

## Project Setup

```bash
mkdir restaurant-list-builder && cd restaurant-list-builder
npm init -y
npm install typescript bottleneck
npm install -D @types/node tsx
```

Add to `package.json`:
```json
{
  "scripts": {
    "scrape": "tsx src/index.ts"
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
    "outDir": "dist",
    "rootDir": "src",
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

Copy `data/us-zip-codes.csv` from the `/google-maps-list-builder` project (same file).

## File Structure

```
restaurant-list-builder/
  data/
    us-zip-codes.csv
  src/
    index.ts        # CLI entry point
    client.ts       # Google Maps client (same as google-maps-list-builder)
    chains.ts       # Chain detection
    score.ts        # Community scoring
    csv.ts          # CSV export
    zips.ts         # Zip loader (same as google-maps-list-builder)
    types.ts        # Types
```

## Core Files

### src/types.ts

```typescript
export interface SearchParams {
  query: string;
  lat?: number;
  lng?: number;
  limit?: number;
  zoom?: number;
  country?: string;
}

export interface Place {
  place_id: string;
  name: string;
  address: string;
  lat: number;
  lng: number;
  rating?: number;
  reviews_count?: number;
  phone?: string;
  website?: string;
  types?: string[];
  category?: string;
}

export interface ScoredRestaurant extends Place {
  is_chain: boolean;
  community_score: number;
  qualifies: boolean;
}
```

### src/chains.ts

Chain detection runs in two passes:
1. **Name match**: check against known chain list
2. **Heuristic**: very high review counts (15,000+) across multiple zip codes usually indicate a chain location

```typescript
const KNOWN_CHAINS = new Set([
  // Fast food
  "mcdonald's", "mcdonalds", "burger king", "wendy's", "wendys", "taco bell",
  "subway", "chipotle", "chick-fil-a", "chickfila", "popeyes", "kfc",
  "jack in the box", "sonic drive-in", "sonic", "whataburger", "five guys",
  "shake shack", "in-n-out", "in n out", "culver's", "culvers", "arby's", "arbys",
  "hardee's", "hardees", "carl's jr", "carls jr", "checkers", "rallys",
  "del taco", "panda express", "wingstop", "raising cane's", "raising canes",
  "freddy's", "freddys", "steak 'n shake", "steak n shake", "cook out",

  // Casual dining chains
  "applebee's", "applebees", "chili's", "chilis", "tgi friday's", "tgi fridays",
  "olive garden", "red lobster", "longhorn steakhouse", "outback steakhouse",
  "outback", "texas roadhouse", "red robin", "buffalo wild wings", "bdubs",
  "denny's", "dennys", "ihop", "perkins", "bob evans", "cracker barrel",
  "waffle house", "village inn", "friendly's", "friendlys", "steak and shake",
  "ruby tuesday", "hooters", "on the border", "el torito", "cheesecake factory",
  "the cheesecake factory", "bj's restaurant", "bjs brewhouse", "yard house",
  "dave & buster's", "dave and busters", "benihana", "pf chang's", "pf changs",

  // Pizza chains
  "pizza hut", "domino's", "dominos", "little caesars", "little caesar's",
  "papa john's", "papa johns", "papa murphy's", "papa murphys", "cicis",
  "cici's pizza", "round table pizza", "godfather's pizza", "godfathers",

  // Coffee / quick-service
  "starbucks", "dunkin'", "dunkin", "dunkin donuts", "tim hortons", "tim horton's",
  "panera bread", "panera", "einstein bros", "einstein bagels", "bruegger's",

  // Sandwiches / fast casual
  "jersey mike's", "jersey mikes", "firehouse subs", "firehouse", "potbelly",
  "jimmy john's", "jimmy johns", "quiznos", "blimpie", "mcalister's", "mcalisters",
  "jason's deli", "jasons deli", "corner bakery",

  // Asian chains
  "p.f. chang's", "noodles & company", "noodles and company", "pei wei",
  "yoshinoya", "genghis grill",

  // Other recognizable chains
  "golden corral", "ryan's grill", "furr's cafeteria", "sizzler",
  "black bear diner", "marie callender's", "marie callenders",
  "shoney's", "shoneys", "big boy", "frisch's big boy",
  "captain d's", "captain ds", "long john silver's", "long john silvers",
  "church's chicken", "churchs chicken", "pollo tropical",
  "el pollo loco", "moe's southwest grill", "moes", "qdoba",
  "baja fresh", "cafe rio", "costa vida",
]);

export function isChain(name: string): boolean {
  const normalized = name.toLowerCase().trim();

  // Direct name match
  if (KNOWN_CHAINS.has(normalized)) return true;

  // Partial match for chains that include location suffix
  // e.g. "McDonald's #1234" or "Starbucks - Times Square"
  for (const chain of KNOWN_CHAINS) {
    if (normalized.startsWith(chain)) return true;
  }

  return false;
}

export function isChainHeuristic(place: { name: string; reviews_count?: number }): boolean {
  if (isChain(place.name)) return true;

  // Very high review counts (15k+) usually mean chain location or tourist trap
  // Community staples rarely exceed 3,000-5,000 reviews
  if ((place.reviews_count ?? 0) > 15000) return true;

  return false;
}
```

### src/score.ts

```typescript
import type { Place, ScoredRestaurant } from './types.js';
import { isChainHeuristic } from './chains.js';

export interface ScoringOptions {
  minRating: number;
  minReviews: number;
}

export function scoreRestaurant(
  place: Place,
  opts: ScoringOptions = { minRating: 4.0, minReviews: 200 }
): ScoredRestaurant {
  const is_chain = isChainHeuristic(place);

  // Community score: higher rating × more reviews = more established
  // ln scale prevents a restaurant with 50k reviews from dominating
  const reviews = place.reviews_count ?? 0;
  const rating = place.rating ?? 0;
  const community_score = reviews > 0 ? Math.round(rating * Math.log(reviews) * 10) / 10 : 0;

  const qualifies =
    !is_chain &&
    rating >= opts.minRating &&
    reviews >= opts.minReviews;

  return { ...place, is_chain, community_score, qualifies };
}
```

### src/csv.ts

```typescript
import { writeFile, mkdir } from 'fs/promises';
import { dirname } from 'path';
import type { ScoredRestaurant } from './types.js';

const HEADERS = [
  'place_id', 'name', 'address', 'phone', 'website',
  'rating', 'reviews_count', 'is_chain', 'community_score',
  'qualifies', 'lat', 'lng', 'category',
];

function escape(val: string | number | boolean | undefined | null): string {
  if (val == null) return '';
  const s = String(val);
  return s.includes(',') || s.includes('"') || s.includes('\n')
    ? `"${s.replace(/"/g, '""')}"`
    : s;
}

export async function exportCSV(restaurants: ScoredRestaurant[], outputPath: string): Promise<void> {
  await mkdir(dirname(outputPath), { recursive: true });
  const lines = [
    HEADERS.join(','),
    ...restaurants.map(r =>
      HEADERS.map(h => escape(r[h as keyof ScoredRestaurant])).join(',')
    ),
  ];
  await writeFile(outputPath, lines.join('\n') + '\n', 'utf-8');
}
```

### src/index.ts

```typescript
import { GoogleMapsClient } from './client.js';
import { exportCSV } from './csv.js';
import { scoreRestaurant } from './score.js';
import { getZipsByState, getZipsByCity, getZipsByMinPopulation } from './zips.js';
import type { Place, ScoredRestaurant } from './types.js';

// Restaurant search queries to run per location
// Running multiple queries and deduping catches more category variations
const RESTAURANT_QUERIES = [
  'local restaurant',
  'family restaurant',
  'diner',
];

function dedup(places: Place[]): Place[] {
  const seen = new Set<string>();
  return places.filter(p => {
    if (seen.has(p.place_id)) return false;
    seen.add(p.place_id);
    return true;
  });
}

function getArg(args: string[], prefix: string): string | undefined {
  const match = args.find(a => a.startsWith(prefix));
  return match ? match.split('=').slice(1).join('=') : undefined;
}

async function main() {
  const args = process.argv.slice(2);

  const zips = getArg(args, '--zips=');
  const cities = getArg(args, '--cities=');
  const state = getArg(args, '--state=');
  const minPop = getArg(args, '--min-pop=');
  const minRating = parseFloat(getArg(args, '--min-rating=') || '4.0');
  const minReviews = parseInt(getArg(args, '--min-reviews=') || '200', 10);
  const communityOnly = args.includes('--community-only');
  const output = getArg(args, '--output=') || './output/restaurants.csv';

  if (!zips && !cities && !state) {
    console.log(`
Restaurant List Builder — Community Staples

Scrapes Google Maps for independent, high-review restaurants.
Filters out chains automatically.

USAGE:
  npm run scrape -- --zips=90210,90211
  npm run scrape -- --state=TX --min-pop=10000
  npm run scrape -- --cities="Austin TX,Dallas TX"
  npm run scrape -- --state=OH --community-only

OPTIONS:
  --zips=ZIP,ZIP       Comma-separated zip codes
  --cities=CITY,CITY   Comma-separated cities (e.g. "Austin TX")
  --state=XX           All zips in a US state (2-letter code)
  --min-pop=N          Filter state zips to population >= N
  --min-rating=N       Minimum star rating (default 4.0)
  --min-reviews=N      Minimum review count (default 200)
  --community-only     Only output restaurants that pass all filters
  --output=PATH        Output CSV path (default ./output/restaurants.csv)

ENVIRONMENT:
  RAPIDAPI_KEY         Your RapidAPI key (required)

OUTPUT COLUMNS:
  name, address, phone, website, rating, reviews_count,
  is_chain, community_score, qualifies, lat, lng

community_score = rating × ln(reviews_count). Score ≥ 20 = established local favorite.
`);
    process.exit(1);
  }

  const apiKey = process.env.RAPIDAPI_KEY;
  if (!apiKey) {
    console.error('Error: RAPIDAPI_KEY environment variable is required');
    process.exit(1);
  }

  const locations: string[] = [];

  if (zips) locations.push(...zips.split(',').map(s => s.trim()));
  if (cities) locations.push(...cities.split(',').map(s => s.trim()));
  if (state) {
    const minPopNum = minPop ? parseInt(minPop, 10) : 0;
    const stateZips = minPopNum > 0
      ? getZipsByMinPopulation(minPopNum, state)
      : getZipsByState(state);
    locations.push(...stateZips.map(z => z.zip));
    console.log(`Loaded ${stateZips.length} zip codes for ${state.toUpperCase()}`);
  }

  console.log(`Scraping restaurants across ${locations.length} location(s)...\n`);

  const client = new GoogleMapsClient({ apiKey, requestsPerSecond: 2 });
  const allPlaces: Place[] = [];

  for (let i = 0; i < locations.length; i++) {
    const loc = locations[i];
    console.log(`  [${i + 1}/${locations.length}] ${loc}`);

    for (const query of RESTAURANT_QUERIES) {
      try {
        const results = await client.search({
          query: `${query} in ${loc}`,
          limit: 20,
          country: 'us',
        });
        allPlaces.push(...results);
      } catch (err: any) {
        console.error(`    Error (${query}): ${err.message}`);
      }
    }
  }

  const unique = dedup(allPlaces);
  console.log(`\nRaw: ${allPlaces.length} → ${unique.length} unique after dedup`);

  const scored: ScoredRestaurant[] = unique
    .map(p => scoreRestaurant(p, { minRating, minReviews }))
    .sort((a, b) => b.community_score - a.community_score);

  const toExport = communityOnly ? scored.filter(r => r.qualifies) : scored;
  const qualifiedCount = scored.filter(r => r.qualifies).length;
  const chainCount = scored.filter(r => r.is_chain).length;

  console.log(`Scored: ${qualifiedCount} pass filters, ${chainCount} chains filtered out`);

  await exportCSV(toExport, output);
  console.log(`Saved to: ${output}`);

  if (scored.length > 0) {
    console.log('\nTop 5 community staples:');
    scored.filter(r => r.qualifies).slice(0, 5).forEach(r => {
      console.log(`  [${r.community_score}] ${r.name} — ${r.rating}★ (${r.reviews_count} reviews)`);
      console.log(`         ${r.address}`);
    });
  }
}

main().catch(err => {
  console.error('Fatal:', err.message);
  process.exit(1);
});
```

Copy `src/client.ts` and `src/zips.ts` directly from `/google-maps-list-builder` — they are identical.

## Running It

```bash
# Community restaurants in Nashville
npm run scrape -- --state=TN --min-pop=5000 --community-only

# Specific zip codes, lower review threshold for smaller towns
npm run scrape -- --zips=37201,37203,37205 --min-reviews=50

# Full output (includes chains for reference), sorted by community score
npm run scrape -- --cities="Memphis TN,Nashville TN,Knoxville TN"
```

## Interpreting the output

| community_score | What it means |
|-----------------|---------------|
| ≥ 25 | Neighborhood institution — decades of repeat customers |
| 20–25 | Solid local favorite — well established |
| 15–20 | Good spot, maybe newer or smaller town |
| < 15 | New or low-volume — not yet a community staple |

**Chain filter note**: The heuristic catches ~95% of chains. Manually review any result with `is_chain = false` but `reviews_count > 8000` — those might be well-loved local icons OR a missed chain.

## What to do next

**For direct restaurant outreach:**
Run `/blitz-list-builder` with the filtered `restaurants.csv` to find owner/manager contacts. Then run `/campaign-copywriting` with a self-storage or local-services angle.

**For community enrichment (recommended for self-storage outreach):**
Take the scored `restaurants.csv` and your self-storage leads CSV and run `/community-anchor-enrichment`. It matches each storage facility to its nearest community staple restaurant and adds `{{anchor_restaurant_name}}` as a personalization field.

## Related skills

- `/google-maps-list-builder` — general-purpose version of this skill
- `/community-anchor-enrichment` — uses this output to enrich a separate business list
- `/self-storage-outreach` — uses the enrichment output in copy
- `/icp-prompt-builder` — required qualification pass before downstream enrichment
