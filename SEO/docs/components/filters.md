# Filter Components

## FilterSidebar

**Location**: `components/catalog/FilterSidebar.tsx`

**Purpose**: Production-ready e-commerce filter interface with multi-select colors, size selection, price ranges, and sorting

**Type**: Client Component (`'use client'`)

**Props**:
```typescript
interface FilterSidebarProps {
  category: string;              // Category slug (e.g., "t-shirts")
  filterCounts: FilterCounts;    // Product counts per filter option
  currentFilters: FilterOptions; // Currently active filters
  availableColors: string[];     // All available colors
  availableSizes: string[];      // All available sizes
  sizeGroups: SizeGroup[] | null; // Optional size grouping configuration
}

interface SizeGroup {
  label: string;    // Group label (e.g., "Kids", "Adult")
  sizes: string[];  // Sizes in this group
}
```

**Key Features**:

1. **Multi-Select Color Filtering**
   - Checkbox per color with product counts
   - Comma-separated URL format: `?color=black,blue`
   - Real-time product count updates
   - Triggers crawl trap warnings when multiple colors selected

2. **Single-Select Size Filtering**
   - Radio button group with visual selection
   - Product counts per size
   - URL format: `?size=M`
   - Supports size grouping (e.g., Kids vs Adult sizes for shoes)
   - Groups display with separators and labels
   - Category-specific size ordering via `size-config.json`

3. **Price Range Filter**
   - Integrated `PriceRangeFilter` component
   - Manual "Apply" button (no auto-updates)
   - Text inputs with $ prefix
   - Shows product price range info
   - URL format: `?price_min=20&price_max=50`

4. **Sort Dropdown**
   - Select component with 5 options
   - Most Popular (default)
   - Price: Low to High / High to Low
   - Name: A-Z / Z-A
   - URL format: `?sort=price_asc`

5. **Product Count Badges**
   - Shows "Black (15)" dynamically
   - Disables options with 0 products
   - Recalculates based on other active filters

6. **Mobile Responsive**
   - Desktop: Fixed sidebar (left column)
   - Mobile: Sheet drawer (slide-in from left)
   - Filter button shows active count: "Filters (3)"

7. **URL State Management**
   - All filter state lives in URL
   - `router.push()` updates URL on change
   - `useEffect` syncs local state with URL changes
   - Clears filters when external navigation resets URL

**State Synchronization**:
```typescript
// Local state syncs with URL on external changes (e.g., clear all button)
useEffect(() => {
  setSelectedColors(currentFilters.colors || []);
  setSelectedSize(currentFilters.size);
  setPriceRange([...]);
  setSortBy(searchParams.get("sort") || "popularity");
}, [currentFilters, searchParams]);
```

**Usage Example**:
```tsx
// Simple category (no size grouping)
<FilterSidebar
  category="t-shirts"
  filterCounts={{
    colors: { black: 15, blue: 12, white: 8 },
    sizes: { S: 10, M: 20, L: 15, XL: 5 },
    priceRange: { min: 19.99, max: 99.99 }
  }}
  currentFilters={{ colors: ['black'], size: 'M' }}
  availableColors={['black', 'blue', 'white', 'red']}
  availableSizes={['S', 'M', 'L', 'XL']}
  sizeGroups={null}
/>

// Category with size grouping (e.g., shoes)
<FilterSidebar
  category="shoes"
  filterCounts={{
    colors: { black: 20, brown: 15 },
    sizes: { '1': 5, '7': 10, '10': 8 },
    priceRange: { min: 49.99, max: 199.99 }
  }}
  currentFilters={{ size: '7' }}
  availableColors={['black', 'brown']}
  availableSizes={['1', '2', '7', '8', '10']}
  sizeGroups={[
    { label: 'Kids', sizes: ['1', '2', '3', '4', '5', '6'] },
    { label: 'Adult', sizes: ['7', '8', '9', '10', '11', '12', '13'] }
  ]}
/>
```

## FilterSummaryBar

**Location**: `components/catalog/FilterSummaryBar.tsx`

**Purpose**: Sticky bar showing all active filters with a single "Clear All" button

**Type**: Client Component (`'use client'`)

**Props**:
```typescript
interface FilterSummaryBarProps {
  category: string;              // For clear all navigation
  filters: FilterOptions;         // Current filter state
  sort?: string;                  // Sort parameter
  page?: number;                  // Current page number
  priceRange: { min: number; max: number }; // Default price range
}
```

**Key Features**:

1. **Always Visible**
   - Sticky positioning (`position: sticky; top: 0`)
   - Shows "Active Filters: None" when no filters applied
   - Z-index ensures it stays above content

2. **Complete Filter Display**
   - Color badges: "Black", "Blue"
   - Size badge: "Size: M"
   - Price badge: "Price: $20-$50"
   - Sort badge: "Sort: Price ↓"
   - Pagination badge: "Page 2"

3. **Single Clear Button**
   - Always visible (disabled when no filters)
   - Resets to base category URL
   - Uses `router.push()` directly (no callback props)

4. **Responsive Layout**
   - Flexbox with flex-wrap for multiple badges
   - Button aligns to right on desktop
   - Stacks vertically on mobile if needed

**Usage Example**:
```tsx
<FilterSummaryBar
  category="t-shirts"
  filters={{ colors: ['black', 'blue'], size: 'M' }}
  sort="price_asc"
  page={2}
  priceRange={{ min: 19.99, max: 99.99 }}
/>
```

## ActiveFilters

**Location**: `components/catalog/ActiveFilters.tsx`

**Purpose**: Display active filters as removable badges in the content area

**Type**: Client Component (`'use client'`)

**Props**:
```typescript
interface ActiveFiltersProps {
  filters: FilterOptions;
  priceRange: { min: number; max: number };
}
```

**Key Features**:

1. **Removable Badges**
   - Each filter shown as a badge with ×
   - Click × to remove individual filter
   - Updates URL immediately

2. **Filter Display**
   - Colors: Individual badge per color
   - Size: "Size: M" badge
   - Price: "Price: $20-$50" badge (only if different from defaults)

3. **Conditional Rendering**
   - Only renders if filters are active
   - Returns null if no filters applied

**Note**: This component provides redundant filter visibility in the content area, complementing the sticky FilterSummaryBar.

## PriceRangeFilter

**Location**: `components/catalog/PriceRangeFilter.tsx`

**Purpose**: Manual price range filtering with explicit "Apply" button for better UX control

**Type**: Client Component (`'use client'`)

**Props**:
```typescript
interface PriceRangeFilterProps {
  initialMin?: number;                          // Current min price from URL
  initialMax?: number;                          // Current max price from URL
  priceRange: { min: number; max: number };     // Available price range from products
  onApply: (min?: number, max?: number) => void; // Callback when user applies filter
  onClear?: () => void;                         // Optional clear callback
}
```

**Key Features**:

1. **Manual Application**
   - User types price values
   - Clicks "Apply Price Filter" button to update URL
   - No auto-debouncing or immediate updates
   - Better UX: user controls when filter applies

2. **Text Input Fields**
   - Numeric inputs with $ prefix
   - Min and Max price fields
   - `inputMode="decimal"` for mobile keyboards
   - Accepts any positive number or empty

3. **Enter Key Support**
   - Press Enter in either field to apply filter
   - Same behavior as clicking Apply button

4. **Visual Feedback**
   - Shows available price range from products
   - Example: "Products range: $19 - $199"
   - Helps users understand valid ranges

5. **State Management**
   - Internal state for input values
   - Syncs with `initialMin`/`initialMax` props
   - Only calls `onApply` when user explicitly applies

**Usage Example**:
```tsx
<PriceRangeFilter
  initialMin={20}
  initialMax={100}
  priceRange={{ min: 19.99, max: 199.99 }}
  onApply={(min, max) => {
    // Update URL with new price range
    updateUrl({ priceMin: min, priceMax: max });
  }}
/>
```

**Why Manual Apply vs Auto-Update**:
- Prevents excessive URL updates while typing
- User can see price inputs before committing
- Better for SEO (fewer history entries)
- More predictable UX (explicit action required)

## GenderFilter

**Location**: `components/catalog/GenderFilter.tsx`

**Purpose**: Gender-based navigation with clean path URLs and SEO education

**Type**: Client Component (`'use client'`)

**Props**:
```typescript
interface GenderFilterProps {
  category: string;                      // Category slug (e.g., "t-shirts")
  currentGender?: string;                // Active gender filter (if on clean path)
  genderCounts: Record<string, number>;  // Product count per gender
  totalCount: number;                    // Total products in category
}
```

**Key Features**:

1. **Large Button Navigation**
   - Five buttons: All, Women, Men, Girls, Boys
   - Active state styling for current selection
   - Prominent size (`size="lg"`) for easy clicking
   - Min-width ensures consistent button sizes

2. **Clean Path URLs**
   - Links to `/shop/[category]/for/[gender]/`
   - "All" button links to base category page
   - SEO-friendly URL structure
   - Statically generated pages

3. **SEO Education Banner**
   - Shows when `currentGender` is set (on clean path page)
   - Green alert with Info icon
   - Explains clean path vs query param approach
   - Highlights SEO benefits: index,follow, sitemap inclusion
   - Educational for developers learning SEO

4. **Responsive Layout**
   - Flexbox with wrapping
   - Buttons stack on mobile if needed
   - Consistent spacing via gap-2

**Usage Example**:
```tsx
// On base category page (no gender filter)
<GenderFilter 
  category="t-shirts"
  genderCounts={{ women: 40, men: 35, girls: 15, boys: 10 }}
  totalCount={100}
/>

// On gender filter page (shows SEO banner)
<GenderFilter 
  category="t-shirts"
  currentGender="women"
  genderCounts={{ women: 40, men: 35, girls: 15, boys: 10 }}
  totalCount={100}
/>
```

**Educational Banner Content**:
```
✓ Clean Path URL: This page uses /shop/t-shirts/for/women/ 
instead of query parameters (?gender=women).

SEO Benefits: Gender is a stable filter that creates meaningful 
landing pages. Clean paths are index,follow and included in sitemaps.
```

