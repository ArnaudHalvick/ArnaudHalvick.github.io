# Configuration

## Project Structure Explained

```
project/
├── app/                          # Next.js 13 App Router
│   ├── layout.tsx               # Root layout (ConfigProvider + SeoReceipt)
│   ├── page.tsx                 # Homepage
│   ├── globals.css              # Global styles + Tailwind imports
│   ├── shop/                   # E-commerce demo pages
│   │   ├── page.tsx            # Shop home / category list
│   │   └── [category]/         # Dynamic category routes
│   │       ├── page.tsx        # Category page with filters
│   │       ├── for/[gender]/   # Gender clean paths
│   │       │   ├── page.tsx    # Gender-filtered category
│   │       │   └── [product]/  # Product pages with gender context
│   │       │       └── page.tsx # Individual product page
│   │       ├── color/[color]/  # Color clean paths
│   │       ├── size/[size]/    # Size clean paths
│   │       └── [product]/      # Legacy route (redirects to gender path)
│   │           └── page.tsx
│   ├── best-practices/         # SEO configuration showcase
│   │   └── page.tsx            # Best practices documentation
│   └── api/                    # API routes
│       ├── robots/             # robots.txt endpoint
│       │   └── route.ts
│       └── sitemap/            # XML sitemap endpoint
│           └── route.ts
│
├── components/                  # React components
│   ├── SeoReceipt.tsx          # Real-time SEO feedback panel
│   ├── DemoChips.tsx           # Parameter demo buttons
│   ├── Navigation.tsx          # Site navigation
│   ├── Breadcrumbs.tsx         # Breadcrumb navigation
│   ├── playground/             # Pattern gallery & best practices components
│   │   ├── PatternCard.tsx     # Individual pattern display
│   │   ├── PatternFilter.tsx   # Pattern filtering and search
│   │   ├── PatternCategory.tsx # Collapsible category sections
│   │   ├── ParamPolicyEditor.tsx
│   │   ├── PaginationSettings.tsx
│   │   ├── RobotsPreview.tsx   # Live robots.txt with annotations
│   │   ├── RobotsTester.tsx    # Interactive URL testing tool
│   │   └── SitemapTable.tsx
│   └── ui/                     # shadcn/ui component library
│       ├── button.tsx
│       ├── card.tsx
│       ├── badge.tsx
│       └── ... (many more)
│
├── lib/                        # Business logic (framework-agnostic)
│   ├── rules/                  # SEO rule engines
│   │   ├── canonical.ts        # Core SEO decision engine
│   │   ├── params.ts           # Parameter classification
│   │   ├── robots.ts           # robots.txt generation
│   │   ├── sitemap.ts          # Sitemap generation
│   │   └── hreflang.ts         # Hreflang logic
│   ├── catalog/                # Demo catalog data access
│   │   └── data.ts
│   ├── config/                 # Configuration management
│   │   └── provider.tsx        # React Context provider
│   ├── meta/                   # Metadata utilities
│   │   └── metadata.ts
│   └── utils/                  # Utility functions
│       ├── url-diff.ts         # URL comparison
│       └── utils.ts            # General utilities
│
├── data/                       # Static JSON data
│   ├── rules.json              # SEO configuration
│   └── catalog.json            # Demo products
│
├── docs/                       # Documentation
│   ├── architecture/
│   ├── components/
│   ├── seo-logic/
│   ├── extending/
│   └── setup/
│
├── public/                     # Static assets
│
├── tailwind.config.ts          # Tailwind CSS configuration
├── tsconfig.json               # TypeScript configuration
├── next.config.js              # Next.js configuration
├── package.json                # Dependencies and scripts
└── README.md                   # Project overview
```

## Configuration Files

### package.json

Defines dependencies and npm scripts:
- `next`: 13.5.1
- `react`: 18.2.0
- `typescript`: 5.2.2
- Many UI and utility libraries

### tsconfig.json

TypeScript configuration:
- Target: ES2017
- Strict mode enabled
- Path aliases: `@/` maps to root

### tailwind.config.ts

Tailwind CSS configuration:
- Content paths for scanning classes
- Theme extensions (colors, animations)
- Plugins: tailwindcss-animate

### next.config.js

Next.js configuration:
- Default settings (mostly)
- Could add redirects, rewrites here

## Environment Setup

### VS Code Configuration

Create `.vscode/settings.json`:

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.tsdk": "node_modules/typescript/lib",
  "tailwindCSS.experimental.classRegex": [
    ["cva\\(([^)]*)\\)", "[\"'`]([^\"'`]*).*?[\"'`]"]
  ]
}
```

### ESLint Configuration

The project uses `.eslintrc.json`:
```json
{
  "extends": "next/core-web-vitals"
}
```

This enables Next.js-specific linting rules.

