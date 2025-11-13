# Catalog Components

## CleanPathPage (Gender Filter)

**Location**: `app/shop/[category]/for/[gender]/page.tsx`

**Purpose**: SEO-friendly clean path route for gender-based filtering

**Type**: Server Component (default)

**Route Pattern**: `/shop/t-shirts/for/women/`

**Key Features**:

1. **Static Generation**
   - Pre-renders pages at build time
   - Uses `generateStaticParams()`
   - 8 pages generated (2 categories × 4 genders)
   - Built alongside product pages for optimal performance

2. **Gender-Aware Size Groups**
   - Automatically filters size groups based on gender
   - Example: Kids sizes for girls/boys, Adult sizes for women/men
   - Uses `getSizeGroupsForGender(category, gender)`
   - Provides contextually relevant size options

3. **Educational Banners**
   - Green alert explaining clean path benefits
   - Compares `/for/women/` vs `?gender=women`
   - Highlights SEO benefits: indexability, sitemap inclusion
   - Educational component: `GenderFilter` with banner

4. **Complete Filtering Support**
   - Gender from URL path (stable filter)
   - Color, size, price from query params
   - All filters work together
   - Breadcrumbs show full navigation path

5. **Parameter Validation**
   - Validates gender against available genders
   - Returns 404 if gender doesn't exist in category
   - Type-safe with TypeScript

**Static Params Generation**:
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

**Routing Note**: The `/for/` prefix creates semantic URLs and prevents conflicts with product routes. It clearly indicates filtering intent.

## CleanPathPage (Color & Size Filters)

**Location**: 
- `app/shop/[category]/color/[color]/page.tsx`
- `app/shop/[category]/size/[size]/page.tsx`

**Purpose**: SEO-friendly clean path routes for color and size filtering

**Type**: Server Component (default)

**Route Patterns**: 
- `/shop/t-shirts/color/black/`
- `/shop/t-shirts/size/M/`

**Key Features**:

1. **Static Generation**
   - Pre-renders pages at build time
   - Uses `generateStaticParams()`
   - 16+ color pages (2 categories × 8+ colors)
   - 20+ size pages (2 categories × 10+ sizes)

2. **Educational Banners**
   - Green alert explaining clean path benefits
   - Compares clean path vs query param approach
   - Shows SEO advantages
   - Links to equivalent query param version

3. **SEO Comparison**
   - Displays SEO Receipt for clean path URL
   - Links to equivalent query param URL
   - Demonstrates URL structure best practices

4. **Parameter Validation**
   - Validates color/size against available options
   - Returns 404 if value doesn't exist
   - Type-safe with TypeScript

**Routing Note**: Prefixes (`/color/`, `/size/`, `/for/`) provide semantic URLs and clear hierarchical structure. Product pages are nested under gender paths (`/shop/t-shirts/for/men/product-slug`) to maintain gender context and provide better SEO signals.

## DemoChips

**Location**: `components/DemoChips.tsx`

**Purpose**: Interactive buttons to test different URL patterns

**Type**: Client Component

**Props**:
- `basePath: string` - Base URL for chip links

**Features**:
1. **Grouped Display**
   - "Common Parameter Types" group
   - "Stacked Parameters" group
   - Clear descriptions for each group

2. **Dynamic Blocking Detection**
   - Checks if robots.txt blocks the pattern
   - Shows "(Blocked)" suffix if matched
   - Uses destructive button variant for blocked

3. **Tooltips**
   - Each chip has a description
   - Explains expected behavior

**Code Structure**:
```typescript
export function DemoChips({ basePath }: DemoChipsProps) {
  const { config } = useConfig();

  const groups = [
    {
      title: "Common Parameter Types",
      chips: [
        { label: "+ Sort (unstable)", params: "sort=price_desc", ... },
        ...
      ]
    },
    ...
  ];

  return (
    <div>
      {groups.map(group => (
        <div key={group.title}>
          <h4>{group.title}</h4>
          {group.chips.map(chip => {
            const isBlocked = checkRobotsBlocking(...);
            return <Button variant={isBlocked ? "destructive" : "outline"}>...</Button>
          })}
        </div>
      ))}
    </div>
  );
}
```

