# Creating Clean Path Routes

To add clean path routes for your new filter type:

## Option 1: Single-Level Clean Path

**File:** `app/shop/[category]/by-brand/[brand]/page.tsx`

```typescript
import { notFound } from 'next/navigation';
import { getCategoryBySlug, filterProducts, getAvailableBrands } from '@/lib/catalog/data';

interface PageProps {
  params: {
    category: string;
    brand: string;
  };
}

export async function generateStaticParams({ params }: { params: { category: string } }) {
  const brands = getAvailableBrands(params.category);
  
  return brands.map(brand => ({
    brand: brand.toLowerCase(),
  }));
}

export default function BrandCleanPathPage({ params }: PageProps) {
  const category = getCategoryBySlug(params.category);
  const availableBrands = getAvailableBrands(params.category);
  
  if (!category || !availableBrands.includes(params.brand)) {
    notFound();
  }
  
  const products = filterProducts(category.products, { brand: params.brand });
  
  return (
    <div>
      <h1>{params.brand} {category.name}</h1>
      {/* Product grid */}
    </div>
  );
}
```

**Route**: `/shop/t-shirts/by-brand/nike/`

**Why `by-brand/` prefix?** Prevents routing conflicts with `[product]` routes, just like `/for/`, `/color/`, and `/size/`.

## Option 2: Two-Level Clean Path (Advanced)

**File:** `app/shop/[category]/by-brand/[brand]/color/[color]/page.tsx`

```typescript
export async function generateStaticParams({ params }) {
  const brands = getAvailableBrands(params.category);
  const colors = getAvailableColors(params.category);
  
  const combinations: { brand: string; color: string }[] = [];
  
  for (const brand of brands) {
    for (const color of colors) {
      const products = filterProducts(
        getProductsByCategory(params.category),
        { brand, color }
      );
      
      // Only generate if products exist for this combination
      if (products.length > 0) {
        combinations.push({ brand, color });
      }
    }
  }
  
  return combinations;
}
```

**Route**: `/shop/t-shirts/by-brand/nike/color/black/`

**SEO Note**: Two-level clean paths should still use `noindex,follow` for the same reasons as query param combinations (N×M bloat). See [routing architecture](../architecture/routing.md) for more details.

