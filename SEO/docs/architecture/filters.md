# Filter System Architecture

## Component Hierarchy

```
FilterSidebar (client component)
├── Color Checkboxes (multi-select)
│   ├── Checkbox per color
│   └── Product count per color
├── Size Radio Buttons (single-select)
│   ├── RadioGroupItem per size
│   ├── Product count per size
│   ├── Optional: Size group labels
│   └── Optional: Size group separators
├── Price Range Filter (manual apply)
│   ├── PriceRangeFilter component
│   ├── Min/Max text inputs
│   └── "Apply" button
├── Sort Dropdown (single-select)
│   └── Select component with options
└── Clear All Button
    └── Resets to base category URL
```

## State Management Flow

1. **User interacts** with filter UI (checkbox, slider, dropdown)
2. **Local state updates** immediately (optimistic UI, instant feedback)
3. **Debounce timer** starts (for price slider only, 500ms delay)
4. **URL updates** via `router.push()` with new search params
5. **Next.js re-renders** page with new searchParams prop
6. **Server component** fetches filtered products from data layer
7. **Product grid updates** with new results
8. **Filter counts recalculate** based on new product set

## URL Synchronization

All filter state lives in the URL as search parameters:

- `?color=black,blue` - Multi-select colors (comma-separated)
- `?size=M` - Single size selection
- `?price_min=20&price_max=50` - Numeric range filters
- `?sort=price_asc` - Sort option
- `?page=2` - Pagination state

**Benefits**:
- ✅ Shareable URLs - Users can share filtered views
- ✅ Browser back/forward works - Navigation history preserved
- ✅ SEO-analyzable - Crawlers see real URLs
- ✅ No client state to sync - URL is single source of truth
- ✅ Deep linking - Direct access to any filtered state

## Filter Count Calculation

`getFilterCounts()` function computes available options dynamically:

```typescript
// Exclude current color filter to show all color options
const filtersWithoutColors = { ...currentFilters, colors: undefined };
const productsForColorCount = filterProducts(allProducts, filtersWithoutColors);

// Count products per color
productsForColorCount.forEach(product => {
  colorCounts[product.color] = (colorCounts[product.color] || 0) + 1;
});
```

This ensures filter options show accurate product counts based on other active filters.

## Price Filter Strategy

Price filter uses manual application instead of auto-debouncing:

```typescript
const handlePriceApply = (minPrice?: number, maxPrice?: number) => {
  updateUrl({
    colors: selectedColors,
    size: selectedSize,
    priceMin: minPrice,
    priceMax: maxPrice,
    sort: sortBy,
  });
};
```

**Why Manual Apply**:
- User controls when filter updates
- No excessive URL updates while typing
- Fewer browser history entries (better for SEO)
- More predictable UX (explicit action required)
- Can review values before applying

## Size Configuration System

### Purpose

The size configuration system allows each category to define custom size ordering and optional grouping for better UX.

### Configuration File Structure

**File**: `data/size-config.json`

```json
{
  "t-shirts": {
    "sizeOrder": ["XS", "S", "M", "L", "XL"],
    "groups": null
  },
  "shoes": {
    "sizeOrder": ["1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", "12", "13"],
    "groups": [
      {
        "label": "Kids",
        "sizes": ["1", "2", "3", "4", "5", "6"]
      },
      {
        "label": "Adult",
        "sizes": ["7", "8", "9", "10", "11", "12", "13"]
      }
    ]
  }
}
```

### Key Concepts

**Size Ordering**:
- Defines the display order for size filters
- Overrides default alphabetical sorting
- Example: `["XS", "S", "M", "L", "XL"]` ensures correct size progression

**Size Grouping** (optional):
- Groups related sizes together visually
- Adds separators and labels in UI
- Example: Kids (1-6) vs Adult (7-13) for shoes
- Can be `null` if no grouping needed

### Gender-Aware Size Groups

For categories with size groups, the system can filter groups by gender:

```typescript
getSizeGroupsForGender('shoes', 'girls')  // Returns only Kids group
getSizeGroupsForGender('shoes', 'women')  // Returns only Adult group
getSizeGroupsForGender('shoes', undefined) // Returns all groups
```

**Logic**:
- Children genders (girls, boys) → Kids sizes only
- Adult genders (women, men) → Adult sizes only
- No gender filter → All size groups shown

**Benefits**:
- Contextually relevant size options
- Reduces clutter in filter UI
- Better UX for gender-specific product pages

