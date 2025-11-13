# Performance and Build

## Performance Considerations

### Static Generation

Most pages are **statically generated** at build time:
- Catalog pages (all categories and products)
- Documentation pages
- Homepage

### Client-Side Rendering

Only interactive components run client-side:
- SeoReceipt (needs URL access)
- DemoChips (needs interactivity)
- Configuration display (minimal logic)

### Code Splitting

Next.js automatically code-splits:
- Each page is a separate bundle
- Shared code in common chunks
- UI components loaded on demand

## Build and Runtime

### Build Time

```
npm run build
    ↓
Next.js compiles TypeScript
    ↓
Generates static pages for:
  - / (homepage)
  - /catalog
  - /catalog/[category] (SSG)
  - /catalog/[category]/[product] (SSG)
    ↓
Generates API routes
```

### Runtime

```
User Request
    ↓
Next.js Server
    ↓
- Static pages served from build
- API routes execute server-side
- Client components hydrate
    ↓
React hydration completes
    ↓
Client-side interactions enabled
```
