# Canonical Strategy and Best Practices

## Canonical vs Robots.txt

### Canonical URL Strategy

**Purpose**: Consolidate signals to the preferred version

**Method**: `<link rel="canonical" href="...">`

**Advantages**:
- Google can still crawl the URL
- Links are discovered
- Signals consolidate to canonical
- Flexible and safe

**Use For**:
- Unstable parameters (sort, view, etc.)
- Pagination (page 2+)
- Search results
- Stacked parameter combinations

### robots.txt Blocking

**Purpose**: Prevent crawling entirely

**Method**: `Disallow: /pattern`

**Advantages**:
- Saves crawl budget
- Prevents parameter explosion
- Blocks unwanted discovery

**Disadvantages**:
- ⚠️ Can prevent link discovery
- ⚠️ May hide indexable stable facets
- ⚠️ Reduces crawl flexibility

**Use For**:
- Protected routes (e.g., `/account/` - demonstrated conceptually)
- Tracking parameters (utm_*, gclid, etc.)
- UI preferences (view, per_page)
- Calendar date explosions

**DO NOT Use For**:
- ❌ Sort parameters (prevents deep pagination discovery)
- ❌ Stacked stable+unstable (hides stable facets)
- ❌ Pagination (prevents page 2+ discovery)

## Decision Precedence

When multiple rules apply, precedence is:

1. **Protected Routes** (highest)
   - Protected routes (e.g., `/account/`) → noindex,nofollow + robots block

2. **Pagination**
   - Page 2+ → noindex,follow

3. **Multi-Select Parameters**
   - Comma-separated values → robots.txt block (no meta tag)
   - Takes precedence over everything except protected routes

4. **Multi-Filter Logic**
   - 2+ stable parameters → noindex,follow
   - Overrides individual parameter policies

5. **Unstable Parameters**
   - Any unstable → noindex,follow (if not already noindex from multi-filter)

6. **Search Pages**
   - Search pages (e.g., `/search`) → noindex,follow

7. **robots.txt Blocking for Tracking Params**
   - Applied to tracking params (utm_, gclid, etc.)
   - Doesn't change robots directive

**Example 1: Multi-Select**:
```
URL: /shop/t-shirts?color=black,blue

Step 3: Not pagination
Step 4: Not protected route
Step 6: Multi-select detected → robots.txt block ✓

Result:
- Robots: undefined (no meta tag needed)
- Blocked: YES (robots.txt)
- Sitemap: EXCLUDED
- Short circuit: Other steps skipped
```

**Example 2: Multiple Stable Filters**:
```
URL: /shop/t-shirts/?color=black&size=m

Step 3: Not pagination
Step 4: Not protected route
Step 6: No multi-select
Step 7: 2 stable params → noindex,follow ✓

Result:
- Robots: noindex,follow
- Blocked: NO
- Sitemap: EXCLUDED
- Canonical: /shop/t-shirts/ (drops both params)
```

**Example 3: Single Stable Filter**:
```
URL: /shop/t-shirts?color=black

Step 3: Not pagination
Step 4: Not protected route
Step 6: No multi-select
Step 7: Only 1 stable param → no action
Step 8: No unstable params

Result:
- Robots: index,follow
- Blocked: NO
- Sitemap: INCLUDED
- Canonical: /shop/t-shirts/?color=black (self)
```

## Best Practices Demonstrated

### 1. Use noindex,follow for Duplicate Content

**Good**:
```
URL: /catalog/t-shirts?sort=price_desc
Robots: noindex,follow
Canonical: /catalog/t-shirts/
```

**Why**: Allows discovery, prevents indexing, consolidates signals.

### 2. Block Tracking Params in robots.txt

**Good**:
```
robots.txt: Disallow: /*?*utm_source=*
URL: /catalog/t-shirts?utm_source=fb
Robots: noindex,follow
Blocked: YES
```

**Why**: Saves crawl budget, prevents parameter explosion.

### 3. Self-Canonical for Pagination

**Good**:
```
URL: /catalog/t-shirts?page=2
Canonical: /catalog/t-shirts/?page=2 (self)
Robots: noindex,follow
```

**Why**: Each page maintains its identity while consolidating signals.

### 4. Don't Block Sort in robots.txt

**Bad**:
```
robots.txt: Disallow: /*?*sort=*
```

**Why**: Can prevent discovery of products only on page 2+ of sorted views. Use noindex,follow instead.

### 5. Include Only Indexable Pages in Sitemap

**Rule**:
```
Sitemap Inclusion = index,follow AND NOT blocked
```

**Why**: Sitemaps should only contain pages you want indexed.

## Debugging SEO Decisions

The Receipt's "Rule Trace" tab shows the complete decision flow:

```
═══ SEO DECISION FLOW ═══
Input URL: /catalog/t-shirts/?sort=price_desc

Step 1: Normalize path → /catalog/t-shirts/
Step 2: Classify parameters
  Stable: none
  Unstable: sort
  Blocked: none
  Search: none
  Pagination: no

Step 3: Detect pagination
  Page 1 or no pagination

Step 4: Check protected routes
  Not a protected/special route

Step 5: Apply parameter policies
  ✓ Unstable params present → noindex,follow
  ✓ Excluded from sitemap

Step 6: Check robots.txt blocking
  Not blocked by robots.txt

Step 7: Build canonical URL
  Dropped unstable params: sort
  Final canonical: https://example.com/catalog/t-shirts/

Step 8: Determine sitemap inclusion
  ✗ Excluded from sitemap (noindex)

═══ FINAL RESULTS ═══
Canonical URL: https://example.com/catalog/t-shirts/
Robots: noindex,follow
Blocked by robots.txt: NO
Sitemap inclusion: EXCLUDED
```

This trace makes the decision process completely transparent and debuggable.

