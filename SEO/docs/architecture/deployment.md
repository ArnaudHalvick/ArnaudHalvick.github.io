# Deployment and Extensibility

## Deployment

The app can be deployed to any Next.js-compatible platform:

- **Vercel** (recommended): Zero-config deployment
- **Netlify**: Next.js support via plugin
- **Self-hosted**: Node.js server required
- **Static export**: Limited (no API routes)

No database or external services required - fully self-contained.

## Extensibility Points

### Adding New Parameter Types

1. Add rule to `data/rules.json`
2. Optionally add logic in `lib/rules/params.ts`
3. No UI changes needed (automatically picked up)

### Adding New Robots.txt Patterns

The app uses a static best-practice model. To add new robots.txt patterns:

1. Add parameter to `data/rules.json` with `policy: "blocked"` for parameters that should be blocked
2. Or add custom logic in `lib/rules/technical-seo/robots.ts` → `checkRobotsBlocking()` for path-based patterns
3. Pattern will appear in robots.txt and SEO Receipt automatically

### Adding New Pages

1. Create page in `app/[route]/page.tsx`
2. SEO logic automatically applies
3. Add to sitemap in `lib/rules/technical-seo/sitemap.ts` if needed

## Error Handling

### TypeScript Type Safety

All functions have explicit types:
- Input validation at compile time
- No runtime type errors
- Clear interfaces for data structures

### Graceful Degradation

If configuration fails to load:
- Falls back to `DEFAULT_PARAM_CONFIG`
- App continues to function
- Warning logged to console

### URL Parsing

Malformed URLs are handled safely:
- URLSearchParams handles all edge cases
- Empty params treated as absent
- No crashes on invalid input

## Testing Strategy

### Type Checking

```bash
npm run typecheck
```

TypeScript ensures:
- All interfaces match
- No type errors
- Correct function signatures

### Build Test

```bash
npm run build
```

Build verifies:
- All imports resolve
- No syntax errors
- All pages generate successfully

### Manual Testing

Use the demo chips to verify:
- Each parameter combination
- Robots.txt patterns
- Sitemap inclusion logic

## Crawl Trap Risk Assessment

### Risk Level Determination

The `getCrawlTrapRisk()` function in `SeoReceipt.tsx` analyzes URL patterns:

**🔴 High Risk**:
- Multi-select parameters: `2^N` combinations
- Blocked parameters with examples: `N×M×...` potential combinations
- Infinite ranges: Calendar dates, arbitrary numbers

**🟡 Medium Risk**:
- 2+ stable parameters: `N×M` combinations (e.g., 5 colors × 4 sizes = 20 URLs)
- 3+ parameters (any type): Exponential growth risk
- Single unstable parameter: Creates duplicate content

**🟢 Low Risk**:
- Single stable parameter: Linear growth, manageable
- No parameters: Base page, safe

### Risk Calculation Examples

```typescript
// High risk: Multi-select with math
{
  level: "high",
  message: "⚠️ High crawl trap risk — multi-select creates exponential combinations",
  explanation: `Multi-select creates 2^N combinations. With 3 options: 2^3 = 8 URLs.`,
  robotsTxtSuggestion: `Disallow: /*?*color=*,*`
}

// Medium risk: Multiple stable filters
{
  level: "medium",
  message: "⚠️ Medium crawl trap risk — multiple filters create N×M combinations",
  explanation: `5 colors × 4 sizes = 20 URL variations. Risk of index bloat.`
}
```

### Integration with SEO Receipt

Risk assessment displays prominently in the receipt:

```
Crawl Trap Risk
────────────────
🔴 High Risk
⚠️ Multi-select creates exponential combinations
Explanation: 2^3 = 8 possible URLs
robots.txt: Disallow: /*?*color=*,*
```

