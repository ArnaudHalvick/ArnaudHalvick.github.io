# Data Flow

## SEO Decision Flow

```
User visits URL
      ↓
Next.js Server Side Rendering
      ↓
computeCanonical() called
      ↓
┌──────────────────────────────┐
│  Step 1: Normalize path      │
│  Step 2: Classify params     │
│  Step 3: Detect pagination   │
│  Step 4: Check protected     │
│  Step 5: Apply param policy  │
│  Step 6: Check robots.txt    │
│  Step 7: Build canonical     │
│  Step 8: Decide sitemap      │
└──────────────────────────────┘
      ↓
CanonicalResult {
  canonical: string
  robots: RobotsDirective
  blockInRobots: boolean
  sitemapIncluded: boolean
  trace: string[]
  warnings: string[]
}
      ↓
SeoReceipt displays results
```

## Configuration Flow

```
data/rules.json (static defaults)
        ↓
ConfigProvider loads on mount
        ↓
localStorage (browser-side only)
        ↓
React Context API
        ↓
Components access via useConfig()
```

## Component Interactions

### SeoReceipt Component

```
SeoReceipt (Client Component)
    ↓
usePathname() + useSearchParams()
    ↓
useConfig() → gets configuration
    ↓
computeCanonical(pathname, params, config)
    ↓
diffUrls(inputUrl, canonicalUrl)
    ↓
Displays: Canonical, Robots, Sitemap, Trace
```

**URL Diff Utility** (`lib/utils/url-diff.ts`):
- `parseUrl(urlString)`: Parses URL into base path and parameter map
- `diffUrls(inputUrl, canonicalUrl)`: Compares two URLs and returns diff segments
  - Returns `baseChanged`: boolean indicating if pathname changed
  - Returns `segments`: Array of `UrlDiffSegment` objects with `text` and `type` ('same' | 'removed' | 'added')
  - Used by `SeoReceipt` to highlight differences between input URL and canonical URL
  - Visual representation: removed params shown in red, added params in green

### DemoChips Component

```
DemoChips (Client Component)
    ↓
useConfig() → gets configuration
    ↓
For each demo:
  checkRobotsBlocking(path, params, config)
    ↓
Render chips with "(Blocked)" suffix if matched
```

### Best Practices Page

```
BestPracticesPage (Client Component)
    ↓
useConfig() → gets configuration
    ↓
Display tabs:
  - ParamPolicyEditor (static display)
  - PaginationSettings (static display)
  - RobotsPreview (live-generated robots.txt with annotations)
  - RobotsTester (interactive URL pattern testing)
  - SitemapTable (static display)
```

### GenderFilter Component

```
GenderFilter (Client Component)
    ↓
Receives props:
  - category (slug)
  - currentGender (if on clean path)
  - genderCounts (products per gender)
  - totalCount
    ↓
Renders navigation buttons:
  - All → /shop/[category]
  - Women → /shop/[category]/for/women
  - Men → /shop/[category]/for/men
  - Girls → /shop/[category]/for/girls
  - Boys → /shop/[category]/for/boys
    ↓
If currentGender set:
  Show SEO education banner
  (clean path vs query param comparison)
```

### Category Page with Filters

```
CategoryPage (Server Component)
    ↓
Parse URL: category slug + search params
    ↓
Fetch data:
  - getCategory(slug)
  - getProductsByCategory(slug)
  - getAvailableColors(slug)
  - getAvailableSizes(slug)
  - getSizeGroups(slug)
  - getGenderCounts(slug)
    ↓
Filter products:
  - filterProducts(products, filters)
  - sortProducts(products, sort)
  - paginateProducts(products, page)
    ↓
Render:
  - GenderFilter (navigation)
  - FilterSummaryBar (sticky, shows active filters)
  - FilterSidebar (color, size, price, sort)
  - Product grid
```

### Gender Clean Path Page

```
GenderFilterPage (Server Component)
    ↓
Parse URL: category slug + gender + search params
    ↓
Validate gender exists in category
    ↓
Fetch data with gender context:
  - getSizeGroupsForGender(category, gender)
    (returns gender-appropriate size groups)
    ↓
Filter products including gender:
  - filters = { gender, colors, size, priceMin, priceMax }
  - filterProducts(products, filters)
    ↓
Render:
  - GenderFilter (shows SEO banner)
  - FilterSummaryBar
  - FilterSidebar (with filtered size groups)
  - Product grid
```

## API Routes

### `/api/technical-seo/robots`

**Type**: Dynamic Route (Server-side)

**Purpose**: Generate robots.txt

**Flow**:
```
GET /api/technical-seo/robots
    ↓
Load DEFAULT_PARAM_CONFIG
    ↓
generateRobotsTxt(config)
    ↓
Return text/plain response
```

### `/api/technical-seo/sitemap`

**Type**: Dynamic Route (Server-side)

**Purpose**: Generate XML sitemap

**Flow**:
```
GET /api/technical-seo/sitemap
    ↓
Load DEFAULT_PARAM_CONFIG
    ↓
generateSitemapEntries(config)
    ↓
generateSitemapXml(entries)
    ↓
Return application/xml response
```
