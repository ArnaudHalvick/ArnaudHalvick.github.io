# Component Patterns and Best Practices

## Client vs Server Components

**Server Components** (default in App Router):
- Product pages
- Catalog pages
- Static content pages

**Client Components** (`'use client'`):
- SeoReceipt (needs hooks)
- DemoChips (needs interactivity)
- All playground components (need client-side logic)

## Configuration Access

Components that need config use the `useConfig()` hook:

```typescript
'use client';

import { useConfig } from '@/lib/config/provider';

export function MyComponent() {
  const { config } = useConfig();
  // Use config...
}
```

## Prop Drilling Avoidance

Instead of passing config through multiple levels, we use React Context:

```
ConfigProvider (top level)
    ↓
useConfig() hook
    ↓
Any component that needs config
```

## Responsive Design

Components use Tailwind's responsive prefixes:

```tsx
<div className="hidden lg:block">Desktop only</div>
<div className="lg:hidden">Mobile only</div>
```

Breakpoints:
- `sm`: 640px
- `md`: 768px
- `lg`: 1024px (main breakpoint for Receipt)
- `xl`: 1280px
- `2xl`: 1536px

## Accessibility

All components follow accessibility best practices:
- Proper ARIA labels
- Keyboard navigation support
- Focus management
- Screen reader friendly

## Component Testing

### Manual Testing

Use the app to verify:
- SeoReceipt updates on URL change
- DemoChips show correct blocking status
- All playground tabs display correctly
- Mobile layout works properly

### Type Safety

TypeScript ensures:
- Correct props passed
- Config structure matches
- No undefined access

```bash
npm run typecheck
```

### Build Verification

```bash
npm run build
```

Verifies all components compile correctly.

## Adding New Components

### Step 1: Create Component File

```typescript
// components/MyComponent.tsx
'use client'; // If using hooks

import { useConfig } from '@/lib/config/provider';

interface MyComponentProps {
  title: string;
}

export function MyComponent({ title }: MyComponentProps) {
  const { config } = useConfig();

  return (
    <div>
      <h2>{title}</h2>
      {/* Component content */}
    </div>
  );
}
```

### Step 2: Import and Use

```typescript
// app/my-page/page.tsx
import { MyComponent } from '@/components/MyComponent';

export default function MyPage() {
  return <MyComponent title="Hello" />;
}
```

### Step 3: Add Types

Ensure TypeScript types are properly defined for all props and return values.

## Component Best Practices

1. **Single Responsibility**: Each component should do one thing well
2. **Small Components**: Break down large components into smaller ones
3. **Type Everything**: Use TypeScript for all props and state
4. **Descriptive Names**: Use clear, descriptive component names
5. **Documentation**: Add JSDoc comments for complex components
6. **Avoid Props Drilling**: Use Context for shared state
7. **Memoization**: Use React.memo() for expensive renders (when needed)
8. **Error Boundaries**: Handle errors gracefully

