# Modifying SEO Logic

## Customizing Crawl Trap Detection

The crawl trap risk assessment logic is in `components/SeoReceipt.tsx` → `getCrawlTrapRisk()`.

### Adding Custom Risk Patterns

```typescript
function getCrawlTrapRisk(
  inputUrl: string,
  result: CanonicalResult,
  evaluated: EvaluatedParams
): CrawlTrapRisk | null {
  // ... existing multi-select detection ...
  
  // Add custom pattern: Date range detection
  const hasDateRange = 
    searchParams.has('start_date') && 
    searchParams.has('end_date');
  
  if (hasDateRange) {
    return {
      level: "high",
      icon: "🔴",
      message: "⚠️ High crawl trap risk — date ranges create infinite URL combinations",
      explanation: "Date parameters like ?start_date=2024-01-01&end_date=2024-12-31 can generate millions of URLs. Consider blocking via robots.txt or using a date picker that limits to specific presets.",
      robotsTxtSuggestion: `Disallow: /*?*start_date=*`
    };
  }
  
  // ... rest of function ...
}
```

### Adding Risk Levels

You can add new risk levels between medium and high:

```typescript
// Medium-High Risk: Single unstable + pagination
if (evaluated.unstableParams.size === 1 && evaluated.pagination.isPaginated) {
  return {
    level: "medium-high",  // Custom level
    icon: "🟠",
    message: "Medium-high crawl trap risk — unstable param with pagination",
    explanation: "Sorting with pagination creates N×P URLs. Example: 5 sorts × 10 pages = 50 URLs."
  };
}
```

Then update the color coding in the render section:

```tsx
const getRiskColor = (level: string) => {
  switch (level) {
    case "high": return "text-red-600 bg-red-50 border-red-200";
    case "medium-high": return "text-orange-600 bg-orange-50 border-orange-200";
    case "medium": return "text-yellow-600 bg-yellow-50 border-yellow-200";
    case "low": return "text-green-600 bg-green-50 border-green-200";
    default: return "text-gray-600 bg-gray-50 border-gray-200";
  }
};
```

## Modifying SEO Logic

The core SEO logic is in `lib/rules/canonical.ts` → `computeCanonical()`.

### Example: Add a New Decision Step

```typescript
export function computeCanonical(...): CanonicalResult {
  // ... existing steps 1-10 ...
  
  // Add Step 11: Custom Logic
  trace.push(`Step 11: Check custom condition`);
  if (pathname.startsWith('/special/')) {
    robots = 'noindex,follow';
    sitemapIncluded = false;
    trace.push(`  ✓ Special path detected → noindex,follow`);
  }
  trace.push('');
  
  // ... rest of function ...
}
```

### Modifying Existing Steps

Each step is clearly marked with comments:

```typescript
// Step 3: Detect pagination
if (evaluated.pagination.isPaginated && evaluated.pagination.pageNumber >= 2) {
  // Your modification here
}
```

### Testing Logic Changes

1. Make changes to `canonical.ts`
2. Save file (hot reload works)
3. Navigate to test URL
4. Check "Rule Trace" tab in Receipt
5. Verify new logic appears in trace

See [SEO Logic documentation](../seo-logic/overview.md) for details on the decision flow.

