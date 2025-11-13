# Decision Steps

## Step 1: Normalize Path

**Purpose**: Ensure consistent URL formatting

**Logic**:
```typescript
function normalizePath(path: string): string {
  let normalized = path.toLowerCase();
  if (!normalized.endsWith('/') && !normalized.includes('?') && normalized !== '/') {
    normalized += '/';
  }
  return normalized;
}
```

**Examples**:
- `/Catalog` → `/catalog/`
- `/CATALOG/T-SHIRTS` → `/catalog/t-shirts/`
- `/catalog` → `/catalog/`

**Why**: Consistency prevents duplicate content from case variations and trailing slash differences.

## Step 2: Classify Parameters

**Purpose**: Categorize each URL parameter

**Classification Types**:

| Type | Behavior | Examples |
|------|----------|----------|
| **Stable** | Indexable, kept in canonical | `color`, `size` |
| **Unstable** | Noindex, dropped from canonical | `sort`, `view`, `per_page` |
| **Blocked** | robots.txt blocked, dropped from canonical | `utm_source`, `gclid` |
| **Search** | Noindex, kept in canonical | `q` |
| **Pagination** | Special handling | `page` |

**Logic**:
```typescript
const evaluated = evaluateParams(path, searchParams, config);
// Returns:
// - stableParams: Map<string, string>
// - unstableParams: Map<string, string>
// - blockedParams: Map<string, string>
// - searchParams: Map<string, string>
// - pagination: { isPaginated: boolean, pageNumber: number }
```

**Example**:
```
Input: /catalog/t-shirts?color=black&sort=price_desc&page=2

Classification:
- Stable: color=black
- Unstable: sort=price_desc
- Pagination: page=2
```

## Step 3: Detect Pagination

**Purpose**: Handle page 2+ with noindex,follow

**Logic**:
```typescript
if (evaluated.pagination.isPaginated && evaluated.pagination.pageNumber >= 2) {
  robots = config.pagination.pageTwoPlus; // "noindex,follow"
  sitemapIncluded = false;
}
```

**Rules**:
- Page 1: Indexable (index,follow)
- Page 2+: noindex,follow
- Page 2+ excluded from sitemap
- Self-canonical strategy (page 2 points to page 2)

**Why**: Page 2+ creates duplicate content. noindex prevents indexing while follow allows link equity flow and content discovery.

## Step 4: Check Protected Routes

**Purpose**: Handle special paths like protected routes and search pages

**Logic**:
```typescript
if (pathname.startsWith('/account/')) {
  robots = 'noindex,nofollow';
  blockInRobots = true;
  sitemapIncluded = false;
}
else if (pathname.startsWith('/search')) {
  robots = 'noindex,follow';
  sitemapIncluded = false;
}
```

**Rules**:
- Protected routes (e.g., `/account/*`): noindex,nofollow + robots block (private data)
- Search pages (e.g., `/search*`): noindex,follow (thin content, allow discovery)

**Note**: The application demonstrates these patterns conceptually. Actual protected routes would follow this strategy.

## Step 5: Check robots.txt Blocking

**Purpose**: Apply robots.txt disallow patterns for tracking parameters

**Logic**:
```typescript
const robotsCheck = checkRobotsBlocking(pathname, searchParams, config);
if (robotsCheck.isBlocked) {
  blockInRobots = true;
  sitemapIncluded = false;
  warnings.push(...robotsCheck.warnings);
}
```

**Blocking Patterns**:
1. **Protected paths**: Patterns like `/account/`, `/api/` (demonstrated conceptually)
2. **Tracking params**: `?utm_source=*`, `?gclid=*`, `?fbclid=*`
3. **UI preferences**: `?view=*`, `?per_page=*`
4. **Price range params**: `?price_min=*`, `?price_max=*` (infinite combinations)

## Step 6: Detect Multi-Select Parameters

**Purpose**: Detect exponential URL combinations from comma-separated values

**Logic**:
```typescript
const multiSelectDetected = Array.from(searchParams.entries())
  .some(([key, value]) => value.includes(','));

if (multiSelectDetected) {
  robots = undefined;  // No meta tag needed (blocked by robots.txt)
  blockInRobots = true;
  sitemapIncluded = false;
  trace.push(`✓ Multi-select detected (exponential combinations) → blocked by robots.txt`);
  trace.push(`  Pattern: Disallow: /*?*color=*,*`);
  trace.push(`  Prevents 2^N URL combinations`);
}
```

**Detection Examples**:
```
?color=black,blue → DETECTED (2^2 = 4 combinations)
?color=black,blue,red → DETECTED (2^3 = 8 combinations)
?color=black → NOT detected (single value)
```

**Why Block via robots.txt?**
- Multi-select creates 2^N exponential growth
- Example: 10 colors = 2^10 = 1,024 URLs
- noindex,follow insufficient (crawlers still access all combinations)
- robots.txt blocking is the ONLY scalable solution

## Step 7: Apply Multi-Filter Logic

**Purpose**: Handle N×M combinations from 2+ stable parameters

**Logic**:
```typescript
const stableParamCount = Array.from(evaluated.stableParams).length;

if (stableParamCount >= 2) {
  robots = 'noindex,follow';
  sitemapIncluded = false;
  trace.push(`✓ Multiple stable filters (${stableParamCount}) → noindex,follow`);
  trace.push(`  Creates N×M combinations, risk of index bloat`);
  trace.push(`  Example: 5 colors × 4 sizes = 20 URL variations`);
}
```

**Examples**:
```
?color=black&size=m → 2 stable params → noindex,follow
5 colors × 4 sizes = 20 URLs → Risk of index bloat

?color=black → 1 stable param → index,follow
Linear growth, manageable
```

**Why noindex,follow (not robots block)?**
- Unlike multi-select, combinations are finite
- Users may want to access specific combinations
- noindex prevents index bloat
- follow allows link discovery
- Canonical consolidates signals to base URL

## Step 8: Apply Parameter Policies

**Purpose**: Enforce unstable parameter behavior

**Logic**:
```typescript
if (evaluated.unstableParams.size > 0 && robots !== 'noindex,follow') {
  robots = 'noindex,follow';
  sitemapIncluded = false;
}
```

**Note**: This step only applies if multi-filter logic hasn't already set noindex.

## Step 9: Build Canonical URL

**Purpose**: Construct the clean, canonical version

**Logic**:
```typescript
for (const [key, value] of evaluated.stableParams.entries()) {
  canonicalParams.set(key, value); // Keep stable params
}

if (evaluated.unstableParams.size > 0) {
  // Drop all unstable params
}

if (evaluated.blockedParams.size > 0) {
  // Drop all blocked params
}

if (evaluated.pagination.isPaginated) {
  if (config.pagination.canonicalStrategy === 'self') {
    canonicalParams.set('page', pageNumber); // Keep pagination
  }
}
```

**Canonical Rules**:
1. **Keep**: Stable parameters
2. **Drop**: Unstable parameters
3. **Drop**: Blocked parameters
4. **Keep**: Search parameters (special case)
5. **Keep**: Pagination (if self-canonical strategy)

**Examples**:
```
Input: /catalog/t-shirts?sort=price_desc&color=black&utm_source=fb
Canonical: https://example.com/catalog/t-shirts/?color=black
Explanation:
  - Kept: color (stable)
  - Dropped: sort (unstable)
  - Dropped: utm_source (blocked)
```

```
Input: /catalog/t-shirts?color=black&size=m
Canonical: https://example.com/catalog/t-shirts/?color=black&size=m
Explanation:
  - Kept: color (stable)
  - Kept: size (stable)
```

```
Input: /catalog/t-shirts?color=black&page=2
Canonical: https://example.com/catalog/t-shirts/?color=black&page=2
Explanation:
  - Kept: color (stable)
  - Kept: page=2 (self-canonical strategy)
```

## Step 10: Determine Sitemap Inclusion

**Purpose**: Decide if URL should be in sitemap

**Logic**:
```typescript
// URL is included in sitemap if ALL conditions are true:
sitemapIncluded =
  robots === 'index,follow' &&  // Must be indexable
  !blockInRobots;                // Not blocked by robots.txt
```

**Inclusion Rules**:

| Scenario | Indexability | Robots Blocked | Sitemap |
|----------|--------------|----------------|---------|
| Base page | index,follow | No | ✅ INCLUDED |
| Stable facet | index,follow | No | ✅ INCLUDED |
| Unstable param | noindex,follow | No | ❌ EXCLUDED |
| Page 2+ | noindex,follow | No | ❌ EXCLUDED |
| Search page | noindex,follow | No | ❌ EXCLUDED |
| Protected route | noindex,nofollow | Yes | ❌ EXCLUDED |
| Tracking param | noindex,follow | Yes | ❌ EXCLUDED |

**Critical Rule**:
```
IF noindex (any variant) → EXCLUDED from sitemap
```

