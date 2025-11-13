# Clean Path Routing Strategy

## File Structure

```
app/shop/[category]/
├── page.tsx                     # Category page with filters
├── [product]/page.tsx           # Legacy route (redirects to gender path)
├── for/[gender]/
│   ├── page.tsx                 # Gender clean path
│   └── [product]/page.tsx       # Product pages with gender context
├── color/[color]/page.tsx       # Color clean path
└── size/[size]/page.tsx         # Size clean path
```

**Important**: Prefixes (`/for/`, `/color/`, `/size/`) are required to avoid routing conflicts. Product pages are nested under gender paths (`/shop/t-shirts/for/men/product-slug`) to provide clear hierarchical structure and gender context for SEO.

## Static Generation

Clean path pages use `generateStaticParams` for pre-rendering:

### Gender Pages
```typescript
export async function generateStaticParams() {
  const categories = getCategories();
  const genders = ['women', 'men', 'girls', 'boys'];
  
  return categories.flatMap(cat => 
    genders.map(gender => ({
      category: cat.slug,
      gender: gender
    }))
  );
}
```

**Result**: 8 gender pages (2 categories × 4 genders)

### Color Pages
```typescript
export async function generateStaticParams({ params }) {
  const colors = getAvailableColors(params.category);
  
  return colors.map(color => ({
    color: color.toLowerCase(),
  }));
}
```

**Result**: 16+ color pages (2 categories × 8+ colors)

### Size Pages
```typescript
export async function generateStaticParams({ params }) {
  const sizes = getAvailableSizes(params.category);
  
  return sizes.map(size => ({
    size: size,
  }));
}
```

**Result**: 20+ size pages (2 categories × 10+ sizes)

### Product Pages

Product pages are nested under gender paths for hierarchical structure:

```typescript
export async function generateStaticParams() {
  const products = getProducts();
  return products.map((product) => ({
    category: product.category,
    gender: product.gender,
    product: product.slug,
  }));
}
```

**Result**: 160 product pages (one per product) at `/shop/{category}/for/{gender}/{product-slug}`

**URL Examples**:
- `/shop/t-shirts/for/men/men-gray-crew-neck-5`
- `/shop/shoes/for/women/women-black-running-sneakers-0`

**SEO Benefits**:
- Gender context in URL path provides topical relevance
- Clear hierarchical structure (Shop > Category > Gender > Product)
- Breadcrumb navigation matches URL structure
- Consistent with gender filter pages (`/for/women/`)

**Legacy Redirect**: The old flat route at `/shop/[category]/[product]/` automatically redirects to the new structure for backwards compatibility.

**Total Static Pages**: 200+ pages (44+ filter pages + 160 product pages) generated at build time

## Path Parameter Validation

Clean paths validate parameters against available options:

### Gender Validation
```typescript
const availableGenders = getAvailableGenders(params.category);
if (!availableGenders.includes(params.gender.toLowerCase())) {
  notFound();  // 404 if gender doesn't exist in category
}
```

### Color Validation
```typescript
const availableColors = getAvailableColors(params.category);
if (!availableColors.includes(params.color)) {
  notFound();  // 404 if color doesn't exist
}
```

### Size Validation
```typescript
const availableSizes = getAvailableSizes(params.category);
if (!availableSizes.includes(params.size)) {
  notFound();  // 404 if size doesn't exist
}
```

### Product Gender Validation
```typescript
const product = getProduct(params.product);
if (product.gender.toLowerCase() !== params.gender.toLowerCase()) {
  notFound();  // 404 if gender in URL doesn't match product's gender
}
```

This ensures URLs always have the correct gender for the product, preventing duplicate content issues.

## Query Params vs Clean Paths

| Aspect | Query Params | Clean Paths |
|--------|-------------|-------------|
| URL | `/shop/t-shirts?gender=women` | `/shop/t-shirts/for/women/` |
| SEO | Good (if single stable filter) | Better (more semantic) |
| UX | Less readable | More readable |
| Flexibility | High (any combination) | Low (pre-defined only) |
| Generation | Dynamic | Static (build time) |
| Indexing | Requires careful canonicals | index,follow by default |
| **Use Case** | Multi-filter, sorting, ranges | Single high-value stable filters |

**Examples of Good Clean Path Candidates**:
- Gender (women, men, girls, boys) - stable, meaningful segments
- Primary color filters - high search volume
- Core size categories - clear user intent

**Keep as Query Params**:
- Multi-select filters (e.g., `?color=black,blue`)
- Sorting and pagination
- Price ranges (infinite combinations)
- Temporary UI state

## Multi-Select Detection Logic

### Detection Method

Comma-separated values in URL parameters indicate multi-select:

```typescript
const multiSelectDetected = Array.from(searchParams.entries())
  .some(([key, value]) => value.includes(','));

// Examples:
// ?color=black,blue → multiSelectDetected = true
// ?color=black      → multiSelectDetected = false
```

### Exponential Combination Math

Multi-select creates 2^N URL combinations:

```
2 colors: black, blue
Combinations: [], [black], [blue], [black,blue]
Total: 2^2 = 4 URLs

3 colors: black, blue, red
Combinations: [], [black], [blue], [red], [black,blue], [black,red], [blue,red], [black,blue,red]
Total: 2^3 = 8 URLs

5 colors selected: 2^5 = 32 URLs
10 colors selected: 2^10 = 1,024 URLs
```

### SEO Treatment

When multi-select is detected:

```typescript
robots = undefined;  // No meta robots tag (crawlers blocked anyway)
blockInRobots = true;  // Block via robots.txt
sitemapIncluded = false;  // Exclude from sitemap

// robots.txt pattern
Disallow: /*?*color=*,*  // Blocks any URL with comma in color param
```

**Rationale**: Exponential combinations waste massive crawl budget. robots.txt blocking is the only scalable solution.

### Integration with canonical.ts

Multi-select detection happens early in the decision flow (Step 6):

```typescript
// Step 6: Check robots.txt blocking (includes multi-select detection)
const multiSelectDetected = Array.from(searchParams.entries())
  .some(([key, value]) => value.includes(','));

if (multiSelectDetected) {
  robots = undefined;
  blockInRobots = true;
  sitemapIncluded = false;
  trace.push(`✓ Multi-select detected → blocked by robots.txt`);
  trace.push(`  Pattern: Disallow: /*?*${param}=*,*`);
}
```

