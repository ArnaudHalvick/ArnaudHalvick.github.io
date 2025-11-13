# Canonical URL Implementation

## Dynamic BaseURL

All canonical URLs use the actual request host instead of hardcoded values:

```typescript
// Get actual host from headers
const headersList = await headers();
const host = headersList.get('host') || 'localhost:3000';
const protocol = headersList.get('x-forwarded-proto') || 'http';
const baseUrl = `${protocol}://${host}`;
```

This ensures canonicals work correctly in:
- Local development (`http://localhost:3000`)
- Staging environments
- Production domains

## Implementation Locations

### Shop Pages (with parameter handling)
- `app/shop/[category]/page.tsx` - Category pages with filters
- `app/shop/[category]/for/[gender]/page.tsx` - Gender-filtered pages
- `app/shop/[category]/for/[gender]/[product]/page.tsx` - Product pages

Use `generatePageMetadata()` which calls `computeCanonical()` to handle parameter-based canonical logic.

### Non-Shop Pages (simple self-referencing)
- All `/technical-seo/*` pages
- All `/strategic-seo/*` pages

Use `generateSimpleMetadata()` for straightforward self-referencing canonical tags.

## Best Practices Applied

1. **Always include canonical tags** - Even for simple pages without parameters
2. **Use actual request host** - Never hardcode domain names
3. **Self-referencing canonicals** - All clean URLs canonical to themselves
4. **Parameter handling** - Stable params kept, unstable params dropped
5. **Protocol awareness** - Respects HTTPS in production via x-forwarded-proto header

