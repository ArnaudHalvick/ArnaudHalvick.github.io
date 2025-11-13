# Adding Pages and Components

## Adding New Pages

### Static Page

Create a simple page:

```tsx
// app/my-page/page.tsx
export default function MyPage() {
  return (
    <div className="container mx-auto px-4 py-12">
      <h1 className="text-4xl font-bold mb-4">My Page</h1>
      <p>Content here...</p>
    </div>
  );
}
```

Access at: http://localhost:3000/my-page

### Dynamic Page

Create a page with dynamic segments:

```tsx
// app/blog/[slug]/page.tsx
interface Props {
  params: {
    slug: string;
  };
}

export default function BlogPost({ params }: Props) {
  return (
    <div>
      <h1>Post: {params.slug}</h1>
    </div>
  );
}

// Generate static params at build time
export function generateStaticParams() {
  return [
    { slug: 'first-post' },
    { slug: 'second-post' },
  ];
}
```

### Adding to Sitemap

Edit `lib/rules/technical-seo/sitemap.ts` → `generateSitemapEntries()`:

```typescript
export function generateSitemapEntries(config: ParamConfig, baseUrl: string): SitemapEntry[] {
  const entries: SitemapEntry[] = [];

  // Add your new pages
  const myPages = [
    { path: '/my-page', priority: 0.8 },
    { path: '/blog/first-post', priority: 0.7 },
  ];

  for (const page of myPages) {
    const result = computeCanonical(page.path, new URLSearchParams(), config, baseUrl);
    entries.push({
      loc: result.canonical,
      priority: page.priority,
      included: result.sitemapIncluded,
      reason: result.sitemapIncluded ? 'Indexable page' : `Excluded: ${result.robots}`,
    });
  }

  // ... existing code ...

  return entries;
}
```

## Adding New Components

### Simple Component

```tsx
// components/MyFeature.tsx
interface MyFeatureProps {
  title: string;
  description: string;
}

export function MyFeature({ title, description }: MyFeatureProps) {
  return (
    <div className="p-4 border rounded-lg">
      <h3 className="font-semibold">{title}</h3>
      <p className="text-sm text-slate-600">{description}</p>
    </div>
  );
}
```

### Client Component with Hooks

```tsx
// components/MyInteractive.tsx
'use client';

import { useState } from 'react';
import { useConfig } from '@/lib/config/provider';

export function MyInteractive() {
  const [count, setCount] = useState(0);
  const { config } = useConfig();

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        Clicked {count} times
      </button>
      <p>Config has {config.rules.length} rules</p>
    </div>
  );
}
```

### Using shadcn/ui Components

```tsx
import { Button } from '@/components/ui/button';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';

export function MyCard() {
  return (
    <Card>
      <CardHeader>
        <CardTitle>My Feature</CardTitle>
      </CardHeader>
      <CardContent>
        <Button>Click Me</Button>
      </CardContent>
    </Card>
  );
}
```

See [Component Patterns](../components/patterns.md) for more guidance on component structure and best practices.

## Customizing UI

### Changing Colors

Edit `tailwind.config.ts`:

```typescript
export default {
  theme: {
    extend: {
      colors: {
        // Add custom colors
        brand: {
          50: '#f0f9ff',
          100: '#e0f2fe',
          // ... more shades
        },
      },
    },
  },
};
```

Use in components:
```tsx
<div className="bg-brand-100 text-brand-900">...</div>
```

### Changing Fonts

Edit `app/globals.css`:

```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');

body {
  font-family: 'Inter', sans-serif;
}
```

### Changing Layout

Edit `app/layout.tsx` to modify the overall layout structure.

### Responsive Breakpoints

Use Tailwind's responsive prefixes:

```tsx
<div className="
  p-4       // mobile
  md:p-6    // tablet
  lg:p-8    // desktop
">
  Content
</div>
```

