# Components Overview

This document describes the key React components used in the application.

## Component Hierarchy

```
App Layout (app/layout.tsx)
├── ConfigProvider (lib/config/provider.tsx)
│   └── Children with config access
└── SeoReceipt (components/SeoReceipt.tsx)
    └── Always visible on right side

Pages (app/**/page.tsx)
├── Homepage (app/page.tsx)
│   └── Featured Interactive Demo section
├── Interactive Demo (app/shop/**) - Renamed from "Shop"
│   ├── Shop Landing (app/shop/page.tsx)
│   ├── Category Page (app/shop/[category]/page.tsx)
│   │   ├── Breadcrumbs
│   │   ├── GenderFilter (gender navigation buttons)
│   │   ├── FilterSummaryBar (sticky filter display)
│   │   ├── FilterSidebar (color, size, price, sort filters)
│   │   └── Product Grid
│   ├── Gender Filter Page (app/shop/[category]/for/[gender]/page.tsx)
│   │   ├── Breadcrumbs
│   │   ├── GenderFilter (with SEO education banner)
│   │   ├── FilterSummaryBar
│   │   ├── FilterSidebar (gender-aware size groups)
│   │   └── Product Grid
│   └── Product Page (app/shop/[category]/for/[gender]/[product]/page.tsx)
│       ├── Breadcrumbs (4-level hierarchy)
│       └── Product details with gender context
└── Educational Pages (app/*/page.tsx)
    ├── Pattern Gallery (app/technical-seo/pattern-gallery/page.tsx)
    ├── SEO Dev Tools (app/technical-seo/seo-dev-tools/page.tsx) - NEW
    ├── Robots (app/technical-seo/robots/page.tsx)
    ├── Sitemap (app/technical-seo/sitemap/page.tsx)
    ├── Parameters (app/technical-seo/parameters/page.tsx)
    │   └── With "Try Demo" callout banner
    ├── Duplicate Content (app/technical-seo/duplicate-content/page.tsx)
    │   └── With "Try Demo" callout banner
    ├── Pagination (app/technical-seo/pagination/page.tsx)
    │   └── With "Try Demo" callout banner
    └── Other SEO learning pages
```

## Core Components

The application uses a component-based architecture with clear separation between:

- **SEO Components**: Real-time SEO analysis and feedback (includes "Test in Demo" button)
- **Filter Components**: E-commerce filtering interface
- **Catalog Components**: Product and category pages
- **Playground Components**: Interactive SEO pattern exploration
- **UI Components**: Reusable shadcn/ui component library
- **Navigation**: Top navigation with "Interactive Demo" link (renamed from "Shop")

Each component is documented in detail in its respective section.

## Recent Updates (November 2025)

- **Navigation**: "Shop" renamed to "Interactive Demo" throughout the app
- **SeoReceipt**: Added "Test in Demo" button for quick navigation to `/shop`
- **Educational Pages**: Added contextual callout banners linking to Interactive Demo
- **Homepage**: Added prominent featured section highlighting the Interactive Demo
- **New Page**: SEO Dev Tools page at `/seo-dev-tools`

