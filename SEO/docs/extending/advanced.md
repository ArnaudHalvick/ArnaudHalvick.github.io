# Advanced Extensions and Contributing

## Adding Tests

### Unit Tests (Manual)

Create a test file:

```typescript
// lib/rules/__tests__/my-feature.test.ts
import { myFunction } from '../my-feature';

describe('myFunction', () => {
  test('returns expected result', () => {
    const result = myFunction('input');
    expect(result).toBe('expected');
  });
});
```

Run with a test runner like Jest (not included by default).

### Type Tests

TypeScript itself acts as a test:

```bash
npm run typecheck
```

### Integration Tests

Create acceptance test file:

```javascript
// test-my-feature.js
const { computeCanonical } = require('./lib/rules/canonical');
const { DEFAULT_PARAM_CONFIG } = require('./lib/rules/params');

const result = computeCanonical('/my-path', new URLSearchParams('my_param=value'), DEFAULT_PARAM_CONFIG);

console.log('Robots:', result.robots);
console.log('Canonical:', result.canonical);
console.log('Sitemap:', result.sitemapIncluded ? 'INCLUDED' : 'EXCLUDED');

// Verify expectations
if (result.robots !== 'index,follow') {
  console.error('FAIL: Expected index,follow');
  process.exit(1);
}

console.log('PASS');
```

Run with:
```bash
node test-my-feature.js
```

## Contributing Guidelines

### Code Style

Follow existing patterns:

1. **TypeScript**: Use explicit types
   ```typescript
   function myFunction(param: string): number {
     return param.length;
   }
   ```

2. **Component Structure**:
   ```typescript
   interface MyComponentProps {
     title: string;
   }

   export function MyComponent({ title }: MyComponentProps) {
     return <div>{title}</div>;
   }
   ```

3. **Naming**:
   - Components: PascalCase (`MyComponent`)
   - Functions: camelCase (`computeCanonical`)
   - Files: kebab-case (`my-component.tsx`) or PascalCase for components
   - Constants: UPPER_SNAKE_CASE (`DEFAULT_CONFIG`)

4. **Imports**:
   ```typescript
   // React/Next first
   import { useState } from 'react';
   import Link from 'next/link';

   // UI components
   import { Button } from '@/components/ui/button';

   // Local imports
   import { myFunction } from '@/lib/utils';
   ```

### Documentation

Add JSDoc comments for complex functions:

```typescript
/**
 * Computes the canonical URL and SEO directives for a given path.
 *
 * @param pathname - The URL pathname (e.g., "/catalog/t-shirts")
 * @param searchParams - URLSearchParams object with query parameters
 * @param config - The SEO configuration object
 * @param baseUrl - Base URL for absolute canonical (default: "https://example.com")
 * @returns CanonicalResult with canonical URL, robots directive, and trace
 */
export function computeCanonical(
  pathname: string,
  searchParams: URLSearchParams,
  config: ParamConfig,
  baseUrl: string = 'https://example.com'
): CanonicalResult {
  // ...
}
```

### Git Workflow

1. Create a feature branch:
   ```bash
   git checkout -b feature/my-feature
   ```

2. Make changes and commit:
   ```bash
   git add .
   git commit -m "Add: My feature description"
   ```

3. Push and create PR:
   ```bash
   git push origin feature/my-feature
   ```

### Commit Messages

Use conventional commits:

```
feat: Add brand parameter support
fix: Correct robots.txt blocking logic
docs: Update setup guide
refactor: Simplify canonical computation
style: Format code with prettier
test: Add tests for pagination logic
```

### Before Submitting

Checklist:
- [ ] Code compiles: `npm run build`
- [ ] Types are correct: `npm run typecheck`
- [ ] Code is formatted (use Prettier)
- [ ] Documentation updated if needed
- [ ] Tested manually in browser
- [ ] No console errors
- [ ] Responsive design works

## Advanced Extensions

### Adding a New SEO Feature

Example: Add structured data support

1. Create new module:
   ```typescript
   // lib/rules/structured-data.ts
   export interface StructuredData {
     type: string;
     data: Record<string, any>;
   }

   export function generateStructuredData(
     pathname: string,
     params: URLSearchParams
   ): StructuredData | null {
     if (pathname.startsWith('/shop/')) {
       return {
         type: 'Product',
         data: {
           '@context': 'https://schema.org',
           '@type': 'Product',
           name: 'Product Name',
         },
       };
     }
     return null;
   }
   ```

2. Integrate with Receipt:
   ```tsx
   // In SeoReceipt.tsx
   const structuredData = generateStructuredData(pathname, params);

   // Display in Receipt
   {structuredData && (
     <div>
       <h3>Structured Data</h3>
       <pre>{JSON.stringify(structuredData, null, 2)}</pre>
     </div>
   )}
   ```

3. Add to page metadata:
   ```tsx
   // In page.tsx
   export async function generateMetadata() {
     const structuredData = generateStructuredData(pathname, params);

     return {
       other: {
         'structured-data': JSON.stringify(structuredData),
       },
     };
   }
   ```

### Adding a New Demo Section

Example: Add internationalization demo

1. Create new page:
   ```tsx
   // app/i18n-demo/page.tsx
   export default function I18nDemo() {
     return <div>Internationalization Demo</div>;
   }
   ```

2. Add to navigation:
   ```tsx
   // In Navigation component
   <Link href="/i18n-demo">
     <Button>I18n Demo</Button>
   </Link>
   ```

3. Add to homepage:
   ```tsx
   // In app/page.tsx
   <Card>
     <CardTitle>Internationalization</CardTitle>
     <Link href="/i18n-demo">
       <Button>View Demo</Button>
     </Link>
   </Card>
   ```

### Performance Optimization

Add memoization for expensive computations:

```typescript
import { useMemo } from 'react';

export function MyComponent() {
  const expensiveResult = useMemo(() => {
    return computeExpensiveThing();
  }, [dependency]);

  return <div>{expensiveResult}</div>;
}
```

### Error Handling

Add error boundaries:

```tsx
// components/ErrorBoundary.tsx
'use client';

import { Component, ReactNode } from 'react';

interface Props {
  children: ReactNode;
}

interface State {
  hasError: boolean;
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <div>Something went wrong</div>;
    }

    return this.props.children;
  }
}
```

## Questions or Issues?

- Check [Architecture](../architecture/overview.md) for system design
- Check [SEO Logic](../seo-logic/overview.md) for decision algorithms
- Check [Components](../components/overview.md) for UI details
- Check [Setup](../setup/installation.md) for environment setup

The codebase is well-documented with TypeScript types and comments. When in doubt, look at existing code for patterns to follow.

