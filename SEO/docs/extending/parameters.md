# Adding Parameters and Robots.txt Patterns

## Adding New Parameter Rules

### Step 1: Define the Rule

Edit `data/rules.json` and add your rule:

```json
{
  "rules": [
    {
      "name": "brand",
      "policy": "stable",
      "description": "Brand filter creates unique product subsets that should be indexed."
    }
  ]
}
```

**Policy Options**:
- `stable` - Indexable, kept in canonical
- `unstable` - Noindex, dropped from canonical
- `blocked` - Robots blocked, dropped from canonical
- `search` - Noindex, kept in canonical
- `pagination` - Special pagination handling

### Step 2: Restart Dev Server

```bash
# Stop with Ctrl+C, then:
npm run dev
```

Configuration is loaded at startup from `data/rules.json`.

### Step 3: Test the Rule

1. Add parameter to URL: `/catalog/t-shirts?brand=nike`
2. Check SEO Receipt
3. Verify correct behavior:
   - Stable: index,follow, in sitemap
   - Unstable: noindex,follow, not in sitemap
   - Blocked: blocked by robots, not in sitemap

### Step 4: Add to Demo Chips (Optional)

Edit `components/DemoChips.tsx`:

```typescript
const groups: ChipGroup[] = [
  {
    title: 'Common Parameter Types',
    chips: [
      {
        label: '+ Brand (stable)',
        params: 'brand=nike',
        description: 'Brand filter → index,follow'
      },
      // ... other chips
    ]
  }
];
```

### Advanced: Custom Parameter Logic

If your parameter needs special handling, edit `lib/rules/params.ts`:

```typescript
export interface ParamRule {
  name: string;
  policy: ParamPolicy;
  description: string;
  mapToPath?: (ctx: { pathname: string; params: URLSearchParams }) => string | null;
}

// Example: Map brand to clean path
{
  name: 'brand',
  policy: 'stable',
  description: '...',
  mapToPath: ({ pathname, params }) => {
    const brand = params.get('brand');
    if (brand && pathname === '/catalog/') {
      return `/catalog/brand/${brand}/`;
    }
    return null;
  }
}
```

## Adding New Robots.txt Patterns

The app follows a **static best-practice model** where parameter policies determine robots.txt behavior automatically.

### Option 1: Parameter-Based Blocking

For URL parameters, add them to `data/rules.json` with `policy: "blocked"`:

```json
{
  "rules": [
    {
      "name": "author",
      "policy": "blocked",
      "description": "Blog author filter. Creates duplicate content, block via robots.txt."
    },
    {
      "name": "tag",
      "policy": "blocked",
      "description": "Tag filter parameter. Block to prevent crawl waste."
    }
  ]
}
```

The system will automatically:
- Generate `Disallow: /*?*author=*` rules in robots.txt
- Block these URLs from crawling
- Show appropriate messages in the SEO Receipt

### Option 2: Path-Based Blocking

For path-specific patterns, add logic to `lib/rules/technical-seo/robots.ts` → `checkRobotsBlocking()`:

```typescript
export function checkRobotsBlocking(
  pathname: string,
  searchParams: URLSearchParams,
  config: ParamConfig
): RobotsMatchResult {
  // ... existing code ...

  // Add custom path-based blocking
  if (pathname.startsWith('/blog/archive/') && searchParams.has('date')) {
    matchedRules.push('Blog Archive: Disallow /blog/archive/*?*date=*');
    isBlocked = true;
  }

  return { isBlocked, matchedRules, warnings };
}
```

### Step 3: Add Warning if Risky

If your pattern might prevent discovery, add a warning:

```typescript
if (toggles.blogFilters?.enabled) {
  if (searchParams.has('author')) {
    matchedRules.push('...');
    isBlocked = true;
    warnings.push('Consider using noindex,follow instead of robots blocking for author pages.');
  }
}
```

### Step 4: Test

1. Restart dev server
2. Visit URL with pattern: `/blog/post?author=john`
3. Check if robots.txt blocks it
4. Verify warning shows in Receipt

