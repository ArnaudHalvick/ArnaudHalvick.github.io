# Architecture Overview

This document explains the system architecture of the SEO Best Practices Showcase application.

## System Overview

The application is built using a **layered architecture** with clear separation of concerns:

```
┌─────────────────────────────────────────────┐
│           Presentation Layer                │
│  (Next.js Pages + React Components)         │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│          Business Logic Layer               │
│    (SEO Rules Engine + Data Processing)     │
└─────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│             Data Layer                      │
│   (Static JSON + LocalStorage Config)       │
└─────────────────────────────────────────────┘
```

## Architecture Layers

### 1. Presentation Layer (`app/` and `components/`)

**Responsibility**: Render UI and handle user interactions

**Key Components**:
- **Pages** (`app/`): Next.js 13 App Router pages
  - Server-side rendering for catalog pages
  - Client-side interactivity for SEO receipt
  - Clean path routes for SEO-friendly URLs
- **UI Components** (`components/`): Reusable React components
  - `SeoReceipt.tsx`: Real-time SEO feedback panel with crawl trap risk
  - `DemoChips.tsx`: Interactive parameter demo buttons
  - `catalog/FilterSidebar.tsx`: Production-ready e-commerce filter interface
  - `catalog/FilterSummaryBar.tsx`: Sticky bar showing all active filters
  - `playground/PatternCard.tsx`: Individual pattern display with SEO impact (used in Pattern Gallery)
  - `playground/PatternFilter.tsx`: Pattern filtering and search (used in Pattern Gallery)
  - `playground/PatternCategory.tsx`: Collapsible category sections (used in Pattern Gallery)
  - `playground/RobotsPreview.tsx`: Live-generated robots.txt with section annotations (Best Practices)
  - `playground/RobotsTester.tsx`: Interactive URL testing for robots.txt patterns (Best Practices)
  - `ui/`: shadcn/ui component library

**Technologies**:
- Next.js 13 (App Router)
- React 18
- TypeScript
- Tailwind CSS

### 2. Business Logic Layer (`lib/`)

**Responsibility**: Core SEO decision-making logic

**Key Modules**:

#### SEO Rules Engine (`lib/rules/`)

The heart of the application, responsible for all SEO decisions:

- **`canonical.ts`**: Main decision engine
  - Computes canonical URLs
  - Detects multi-select parameters (exponential combinations)
  - Handles multi-filter logic (2+ stable parameters)
  - Determines robots directives
  - Decides sitemap inclusion
  - Returns complete decision trace with risk assessment

- **`params.ts`**: Parameter classification
  - Classifies parameters as stable/unstable/blocked/search
  - Evaluates parameter combinations
  - Provides parameter rules and mappings

- **`robots.ts`**: Robots.txt generation
  - Generates robots.txt content with best-practice patterns
  - Checks if URLs match blocking patterns
  - Detects multi-select patterns (comma-separated values)
  - Provides warnings for risky patterns

- **`sitemap.ts`**: Sitemap generation
  - Generates sitemap entries
  - Filters based on indexability
  - Outputs XML format

#### Configuration Provider (`lib/config/`)

- **`provider.tsx`**: React Context for configuration
  - Loads default config from `data/rules.json`
  - Merges with browser localStorage
  - Provides config to all components

#### Catalog Management (`lib/catalog/`)

- **`data.ts`**: Product data and filtering logic
  - Product filtering by color, size, gender, price range
  - Multi-select color support with comma-separated values
  - Filter count calculation for sidebar display
  - Product sorting and pagination
  - Gender-related helper functions:
    - `getAvailableGenders(categorySlug)` - Get all genders in category
    - `getGenderCounts(categorySlug)` - Count products per gender
  - Size configuration functions:
    - `getAvailableSizes(categorySlug)` - Get sizes with category-specific ordering
    - `getSizeGroups(categorySlug)` - Get size grouping config (e.g., Kids/Adult)
    - `getSizeGroupsForGender(categorySlug, gender)` - Filter groups by gender context

#### Metadata Utilities (`lib/meta/`)

- **`metadata.ts`**: Next.js metadata generation utilities
  - `generatePageMetadata(ctx: MetadataContext)`: Generates Next.js Metadata object with robots directives and canonical URL
  - `getTitleForPath(pathname: string)`: Returns SEO-friendly page title based on pathname
  - `getDescriptionForPath(pathname: string)`: Returns SEO-friendly page description based on pathname
  - Integrates with SEO rules engine to ensure metadata matches SEO decisions
  - Used by Next.js page components to set `<head>` metadata

**Usage Example**:
```typescript
import { generatePageMetadata } from '@/lib/meta/metadata';

export async function generateMetadata({ params, searchParams }) {
  const config = await getConfig();
  return generatePageMetadata({
    pathname: params.pathname,
    searchParams: new URLSearchParams(searchParams),
    config,
    baseUrl: 'https://example.com',
  });
}
```

#### Utilities (`lib/utils/`)

- **`url-diff.ts`**: URL comparison and diff highlighting
- **`utils.ts`**: General utility functions

### 3. Data Layer (`data/`)

**Responsibility**: Store static configuration and demo data

**Files**:
- **`rules.json`**: Default SEO configuration
  - Parameter classification rules
  - Pagination policies
  - Robots.txt patterns

- **`catalog.json`**: Demo product data
  - Product listings with gender field
  - Category information
  - Facet values (colors, sizes, genders)
  - 160 products across 2 categories

- **`size-config.json`**: Category-specific size configuration
  - Size ordering per category
  - Optional size grouping (e.g., Kids vs Adult)
  - Used by filter components for display

- **`seo-patterns.json`**: SEO pattern gallery data
  - 17 production-ready SEO patterns
  - Organized by 6 categories (filtering, sorting, ranges, URL strategies, navigation, access control)
  - Each pattern includes: risk level, examples, usage guidance, SEO impact indicators
