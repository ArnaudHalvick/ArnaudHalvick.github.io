# Strategic SEO Section Overview

The Strategic SEO section covers non-programming SEO topics: research, content strategy, production workflows, measurement, and authority building. Unlike the Technical SEO section (which focuses on code implementation), Strategic SEO provides frameworks and best practices for SEO work that doesn't require development resources.

## Path Structure

All Strategic SEO pages are located under `/app/strategic-seo/`:

```
app/strategic-seo/
├── page.tsx                    # Landing page
├── research-strategy/          # Research & Strategy
├── content-architecture/       # Content Architecture
├── content-production/         # Content Production
├── measurement-optimization/   # Measurement & Optimization
├── authority-building/         # Authority Building
├── strategic-tools/            # Strategic Tools
└── resources/                  # Resources & Templates
```

## Section Organization

The landing page (`/strategic-seo/page.tsx`) is organized into three main sections that match the top navigation:

### 1. Strategy & Planning
- **Research & Strategy**: Keyword research, SERP analysis, competitor intelligence
- **Content Architecture**: Hub & spoke architecture, content briefs, E-E-A-T signals

### 2. Production & Growth
- **Content Production**: Editorial workflows, governance, quality frameworks
- **Measurement & Optimization**: KPI design, dashboards, experimentation
- **Authority Building**: Link acquisition, local SEO, reviews, UGC governance

### 3. Tools & Resources
- **Strategic Tools**: Keyword research tools, SERP analysis tools, analytics platforms
- **Resources**: Content brief templates, editorial calendars, KPI dashboards, E-E-A-T checklists

## Design Patterns

Strategic SEO pages follow consistent design patterns:

- **Color Scheme**: Purple/pink gradients (vs. blue for Technical SEO)
- **Layout**: Card-based layouts with icons from `lucide-react`
- **Content Style**: Educational content with checklists, frameworks, and best practices
- **No Interactive Demos**: Unlike Technical SEO, these pages are informational only

## Key Differences from Technical SEO

| Aspect | Technical SEO | Strategic SEO |
|--------|--------------|---------------|
| **Focus** | Code implementation | Frameworks & processes |
| **Color** | Blue gradients | Purple/pink gradients |
| **Demos** | Interactive demos | Educational content |
| **Audience** | Developers | SEO strategists, content teams |
| **Tools** | Dev tools, crawlers | Research tools, templates |

## File Structure

Each page follows a standard Next.js App Router structure:

```typescript
// app/strategic-seo/[page-name]/page.tsx
import { Metadata } from "next";
import { generateSimpleMetadata } from "@/lib/meta/metadata";

export async function generateMetadata(): Promise<Metadata> {
  return generateSimpleMetadata(
    "Page Title - SEO Workshop",
    "Page description",
    "/strategic-seo/[page-name]/"
  );
}

export default function PageName() {
  return (
    <div className="bg-gradient-to-b from-purple-50 to-slate-50 min-h-screen">
      {/* Page content */}
    </div>
  );
}
```

## Navigation Integration

The Strategic SEO section integrates with the main navigation component (`components/Navigation.tsx`). The navigation automatically detects when users are in the Strategic SEO section and shows the appropriate dropdown menus.

See [Navigation](./navigation.md) for detailed navigation structure.

## Adding New Pages

To add a new Strategic SEO page:

1. Create a new folder: `/app/strategic-seo/your-page-name/`
2. Add `page.tsx` with metadata and content
3. Update the landing page (`/app/strategic-seo/page.tsx`) to add a card
4. Update navigation (`components/Navigation.tsx`) to add to appropriate dropdown

See [Pages](./pages.md) for detailed page descriptions and examples.

## Related Documentation

- [Navigation Structure](./navigation.md) - How navigation works in Strategic SEO
- [Pages](./pages.md) - Detailed descriptions of each page
- [Components Overview](../components/overview.md) - UI components used
- [Architecture Overview](../architecture/overview.md) - System architecture

