# SeoReceipt Component

**Location**: `components/SeoReceipt.tsx`

**Purpose**: Display real-time SEO analysis for the current URL

**Type**: Client Component (`'use client'`)

**Props**: None (uses hooks to get current route)

**State**:
- `copiedField`: Tracks which field was copied to clipboard

**Hooks Used**:
- `usePathname()` - Get current pathname
- `useSearchParams()` - Get current URL parameters
- `useRouter()` - Navigation for "Test in Demo" button
- `useConfig()` - Get SEO configuration

**Key Features**:
1. **"Test in Demo" Button** (November 2025)
   - Visible green button in the SEO Receipt header
   - Only appears when NOT on `/shop` routes
   - Navigates to `/shop` (Interactive Demo) for hands-on testing
   - Desktop: Shows "Test in Demo" with icon
   - Mobile: Shows "Test" with icon

2. **URL Comparison**
   - Shows input URL vs canonical URL
   - Highlights differences (removed params in red, added in green)
   - Copy to clipboard buttons

3. **Indexability Display**
   - Badge showing robots directive
   - Icon indicators (green check, yellow warning, red X)
   - Robots.txt blocking status

4. **Sitemap Inclusion**
   - Shows if URL is included in sitemap
   - Explains why included/excluded

5. **Warnings**
   - Shows SEO warnings if any
   - Examples: risky robots.txt patterns

**Responsive Behavior**:
- Desktop (lg+): Fixed panel on right side
- Mobile: Fixed panel at bottom, collapsible

**Code Structure**:
```typescript
export function SeoReceipt() {
  const pathname = usePathname();
  const searchParams = useSearchParams();
  const router = useRouter();
  const { config } = useConfig();

  // Compute SEO decision
  const result = computeCanonical(pathname, params, config);

  // Show "Test in Demo" button only when not on /shop
  const showTestButton = !pathname.includes('/shop');

  // Render two layouts (desktop + mobile)
  return (
    <>
      <div className="hidden lg:block ...">
        {/* Desktop: Button in header */}
        {showTestButton && (
          <Button onClick={() => router.push('/shop')}>
            Test in Demo
          </Button>
        )}
        <SeoReceiptContent {...props} />
      </div>
      <div className="lg:hidden ...">
        {/* Mobile: Button next to title */}
        {showTestButton && <Button>Test</Button>}
        <SeoReceiptContent {...props} />
      </div>
    </>
  );
}
```

## SeoReceiptContent Component

**Location**: `components/seo-receipt/SeoReceiptContent.tsx`

**Purpose**: Separated presentation component that renders the actual SEO receipt content

**Type**: Client Component (`'use client'`)

**Relationship**: `SeoReceipt` handles layout and data fetching, while `SeoReceiptContent` handles the presentation layer for better separation of concerns.

**Props**:
- `inputUrl`: string - The current URL being analyzed
- `result`: CanonicalResult - The SEO decision result from `computeCanonical()`
- `segments`: UrlDiffSegment[] - URL diff segments for highlighting changes
- `isIndexable`: boolean - Whether the page is indexable
- `isNoindexFollow`: boolean - Whether page uses noindex,follow
- `isNoindexNofollow`: boolean - Whether page uses noindex,nofollow
- `inSitemap`: boolean - Whether page is included in sitemap
- `hasPriceParams`: boolean - Whether URL contains price parameters
- `copiedField`: string | null - Which field was copied to clipboard
- `copyToClipboard`: (text: string, field: string) => Promise<void> - Copy handler
- `copyTrace`: () => Promise<void> - Copy trace handler
- `shortExplanation`: string - Short explanation text
- `cleanPathRec`: CleanPathRecommendation | null - Clean path recommendation
- `crawlTrapRisk`: CrawlTrapRisk | null - Crawl trap risk assessment
- `bestPracticeConfirmation`: object | null - Best practice confirmation message
- `compact?`: boolean - Optional compact mode for smaller displays

**Key Features**:
1. **Tabbed Interface**: Two tabs - "Summary" and "Rule Trace"
2. **Summary Tab**: Shows URL comparison, indexability status, sitemap inclusion, warnings, and recommendations
3. **Rule Trace Tab**: Shows complete decision log with copy functionality
4. **Responsive**: Supports compact mode for smaller displays

**Why Separated?**
- **Reusability**: Can be used in different layouts (desktop sidebar, mobile drawer, modal, etc.)
- **Testability**: Easier to test presentation logic separately
- **Maintainability**: Clear separation between data fetching and presentation

