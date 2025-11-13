# Adding Filter Types

## Adding New Filter Types

The filter system is designed to be extensible. Here's how to add a new filter type (e.g., "brand"):

### Step 1: Update Product Data Model

**File:** `lib/catalog/data.ts`

```typescript
export interface Product {
  // ... existing fields
  brand?: string;  // Add new field
}

export interface FilterOptions {
  // ... existing fields
  brand?: string;  // Add to filter options
}
```

Update sample products in `data/catalog.json`:
```json
{
  "id": "1",
  "name": "Classic White T-Shirt",
  "brand": "Nike",
  ...
}
```

### Step 2: Update Filter Function

**File:** `lib/catalog/data.ts`

```typescript
export function filterProducts(products: Product[], filters: FilterOptions): Product[] {
  let filtered = products;
  
  // ... existing filters (color, size, price)
  
  // Add brand filter
  if (filters.brand) {
    filtered = filtered.filter(p => p.brand === filters.brand);
  }
  
  return filtered;
}
```

### Step 3: Add to FilterCounts

```typescript
export interface FilterCounts {
  colors: Record<string, number>;
  sizes: Record<string, number>;
  priceRange: { min: number; max: number };
  brands: Record<string, number>;  // Add this
}

export function getFilterCounts(
  categorySlug: string,
  currentFilters: FilterOptions = {}
): FilterCounts {
  // ... existing code ...
  
  // Count brands
  const brandCounts: Record<string, number> = {};
  const filtersWithoutBrand = { ...currentFilters, brand: undefined };
  const productsForBrandCount = filterProducts(allProducts, filtersWithoutBrand);
  
  productsForBrandCount.forEach(product => {
    if (product.brand) {
      const brand = product.brand.toLowerCase();
      brandCounts[brand] = (brandCounts[brand] || 0) + 1;
    }
  });
  
  return {
    colors: colorCounts,
    sizes: sizeCounts,
    priceRange,
    brands: brandCounts,  // Add this
  };
}

// Add helper function
export function getAvailableBrands(categorySlug: string): string[] {
  const products = getProductsByCategory(categorySlug);
  const brands = new Set<string>();
  
  products.forEach(product => {
    if (product.brand) {
      brands.add(product.brand.toLowerCase());
    }
  });
  
  return Array.from(brands).sort();
}
```

### Step 4: Add to FilterSidebar UI

**File:** `components/catalog/FilterSidebar.tsx`

Add the filter UI component following the same pattern as existing filters. See the [filter components documentation](../components/filters.md) for examples.

### Step 5: Update Category Page

**File:** `app/shop/[category]/page.tsx`

```typescript
export default function CategoryPage({ params, searchParams }: PageProps) {
  // ... existing code ...
  
  const filters: FilterOptions = {
    colors: colorsArray,
    size: searchParams.size,
    brand: searchParams.brand,  // Add this
    priceMin: priceMin,
    priceMax: priceMax,
  };
  
  const availableBrands = getAvailableBrands(params.category);  // Add this
  
  return (
    <div>
      <FilterSidebar
        category={params.category}
        filterCounts={filterCounts}
        currentFilters={filters}
        availableColors={availableColors}
        availableSizes={availableSizes}
        availableBrands={availableBrands}  // Add this
      />
    </div>
  );
}
```

### Step 6: Update SEO Logic (Optional)

**File:** `data/rules.json`

Decide parameter policy:
```json
{
  "rules": [
    {
      "name": "brand",
      "policy": "stable",
      "description": "Brand filter creates unique product subsets that should be indexed."
    }
  ]
}
```

### Step 7: Test

1. Restart dev server: `npm run dev`
2. Navigate to `/shop/t-shirts`
3. Select a brand from the new filter
4. Verify URL updates: `?brand=nike`
5. Check SEO Receipt for correct indexability
6. Verify product counts update correctly

## Adding Size Groups to Categories

Size groups allow you to organize sizes visually in the filter UI. This is useful for categories with diverse size ranges (e.g., shoes with Kids and Adult sizes).

### Step 1: Define Size Configuration

**File:** `data/size-config.json`

Add or update your category configuration:

```json
{
  "your-category": {
    "sizeOrder": ["size1", "size2", "size3", ...],
    "groups": [
      {
        "label": "Group 1 Name",
        "sizes": ["size1", "size2"]
      },
      {
        "label": "Group 2 Name",
        "sizes": ["size3", "size4"]
      }
    ]
  }
}
```

**Example - Clothing with size groups:**
```json
{
  "pants": {
    "sizeOrder": ["26", "28", "30", "32", "34", "36", "38", "40"],
    "groups": [
      {
        "label": "Slim Fit",
        "sizes": ["26", "28", "30", "32"]
      },
      {
        "label": "Regular Fit",
        "sizes": ["34", "36", "38", "40"]
      }
    ]
  }
}
```

**Example - Simple ordering (no groups):**
```json
{
  "t-shirts": {
    "sizeOrder": ["XS", "S", "M", "L", "XL", "XXL"],
    "groups": null
  }
}
```

### Step 2: Understand the Schema

**Required Fields:**
- `sizeOrder`: Array of sizes in display order (overrides alphabetical sorting)
- `groups`: Array of size groups or `null` for no grouping

**Optional Group Fields:**
- `label`: Display name for the group (e.g., "Kids", "Adult")
- `sizes`: Array of sizes in this group (must be subset of `sizeOrder`)

### Step 3: Gender-Aware Groups (Optional)

For categories where size groups should filter by gender, add logic to `lib/catalog/data.ts`:

**Current implementation (shoes):**
```typescript
export function getSizeGroupsForGender(
  categorySlug: string,
  gender?: string
): SizeGroup[] | null {
  const allGroups = getSizeGroups(categorySlug);
  
  if (!allGroups || !gender) {
    return allGroups;
  }
  
  // Filter groups by gender
  if (categorySlug === 'shoes') {
    const isChild = gender === 'girls' || gender === 'boys';
    const isAdult = gender === 'women' || gender === 'men';
    
    if (isChild) {
      return allGroups.filter(group => group.label === 'Kids');
    } else if (isAdult) {
      return allGroups.filter(group => group.label === 'Adult');
    }
  }
  
  return allGroups;
}
```

**Add your category:**
```typescript
// Add after shoes logic
if (categorySlug === 'your-category') {
  const isChild = gender === 'girls' || gender === 'boys';
  
  if (isChild) {
    return allGroups.filter(group => group.label === 'Kids');
  } else {
    return allGroups.filter(group => group.label === 'Adults');
  }
}
```

### Step 4: Use in Components

Size groups are automatically used by `FilterSidebar` when provided:

```typescript
// In your category page
const sizeGroups = getSizeGroupsForGender(params.category, filters.gender);

<FilterSidebar
  category={params.category}
  filterCounts={filterCounts}
  currentFilters={filters}
  availableColors={availableColors}
  availableSizes={availableSizes}
  sizeGroups={sizeGroups}  // Pass size groups here
/>
```

### Step 5: Test

1. Restart dev server: `npm run dev`
2. Navigate to your category page
3. Verify sizes display in correct order
4. If groups exist, verify visual grouping with separators
5. Test gender filter integration (if applicable)

### Best Practices

**When to Use Size Groups:**
- Diverse size ranges (e.g., 1-13 for shoes)
- Logical groupings exist (Kids vs Adult, Small vs Large)
- Improves UX by reducing visual clutter

**When to Skip Groups:**
- Simple size progressions (XS-XXL)
- Small number of sizes (< 6)
- No logical grouping exists

**Size Ordering:**
- Always define `sizeOrder` even without groups
- Ensures logical size progression (not alphabetical)
- Example: ["S", "M", "L"] instead of ["L", "M", "S"]

## Adding Gender Filter Options

Gender filters create clean path URLs for stable product segmentation. Here's how to add or modify gender options.

### Step 1: Update Product Data

Ensure products in `data/catalog.json` have gender field:

```json
{
  "products": [
    {
      "id": "1",
      "title": "Classic T-Shirt",
      "gender": "women",
      ...
    }
  ]
}
```

**Standard gender values:**
- `women` - Adult women's products
- `men` - Adult men's products
- `girls` - Children's products (girls)
- `boys` - Children's products (boys)
- `unisex` - Gender-neutral products (optional)

### Step 2: Update Gender Filter Component (if adding new genders)

**File:** `components/catalog/GenderFilter.tsx`

```typescript
const genders = [
  { key: 'all', label: 'All', href: `/shop/${category}` },
  { key: 'women', label: 'Women', href: `/shop/${category}/for/women` },
  { key: 'men', label: 'Men', href: `/shop/${category}/for/men` },
  { key: 'girls', label: 'Girls', href: `/shop/${category}/for/girls` },
  { key: 'boys', label: 'Boys', href: `/shop/${category}/for/boys` },
  // Add new gender:
  { key: 'unisex', label: 'Unisex', href: `/shop/${category}/for/unisex` },
];
```

### Step 3: Update Static Generation

**File:** `app/shop/[category]/for/[gender]/page.tsx`

Add new gender to static params:

```typescript
export async function generateStaticParams() {
  const categories = getCategories();
  const genders = ['women', 'men', 'girls', 'boys', 'unisex'];  // Add here
  
  return categories.flatMap(cat => 
    genders.map(gender => ({
      category: cat.slug,
      gender: gender
    }))
  );
}
```

### Step 4: Update Gender-Aware Size Groups (if using)

**File:** `lib/catalog/data.ts`

Add logic for new gender in `getSizeGroupsForGender()`:

```typescript
if (categorySlug === 'shoes') {
  const isChild = gender === 'girls' || gender === 'boys';
  const isAdult = gender === 'women' || gender === 'men';
  const isUnisex = gender === 'unisex';
  
  if (isChild) {
    return allGroups.filter(group => group.label === 'Kids');
  } else if (isAdult) {
    return allGroups.filter(group => group.label === 'Adult');
  } else if (isUnisex) {
    return allGroups;  // Show all groups
  }
}
```

### Step 5: Test

1. Rebuild for static generation: `npm run build`
2. Navigate to `/shop/[category]/for/[new-gender]`
3. Verify page renders with correct products
4. Check SEO Receipt shows index,follow
5. Verify gender button shows active state
6. Test size groups filter appropriately (if applicable)

### Best Practices

**Gender Segmentation:**
- Use gender for meaningful product differentiation
- Creates valuable landing pages for SEO
- Examples: "Women's Running Shoes", "Boys' T-Shirts"

**URL Structure:**
- Clean paths: `/shop/shoes/for/women/`
- Semantic "for" prefix indicates targeting
- SEO-friendly and user-friendly

**When to Add Genders:**
- Product has genuine gender differentiation
- Significant product count per gender (10+ products)
- Users search by gender ("women's shoes")

**When NOT to Add Genders:**
- Truly unisex products (most accessories)
- Insufficient products per gender segment
- Gender not relevant to product type

### Advanced: Custom Gender Logic

For categories with unique gender requirements:

```typescript
// In lib/catalog/data.ts
export function getCategoryGenders(categorySlug: string): string[] {
  const genderConfig: Record<string, string[]> = {
    'shoes': ['women', 'men', 'girls', 'boys'],
    't-shirts': ['women', 'men', 'girls', 'boys'],
    'accessories': ['unisex'],  // Only unisex
    'watches': ['women', 'men'],  // No children's watches
  };
  
  return genderConfig[categorySlug] || ['women', 'men', 'girls', 'boys'];
}
```

Then use in `GenderFilter`:
```typescript
const categoryGenders = getCategoryGenders(category);
const genders = [
  { key: 'all', label: 'All', href: `/shop/${category}` },
  ...categoryGenders.map(gender => ({
    key: gender,
    label: gender.charAt(0).toUpperCase() + gender.slice(1),
    href: `/shop/${category}/for/${gender}`
  }))
];
```

