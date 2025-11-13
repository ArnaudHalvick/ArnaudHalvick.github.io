# Development

## Starting the Dev Server

```bash
npm run dev
```

The application will be available at:
- **Local**: http://localhost:3000
- **Network**: http://[your-ip]:3000

The dev server includes:
- Hot module replacement (HMR)
- Fast refresh for React components
- TypeScript type checking
- Tailwind CSS compilation

## Project Commands

```bash
# Development server
npm run dev

# Type checking only (no build)
npm run typecheck

# Production build
npm run build

# Start production server (after build)
npm start

# Lint code
npm run lint
```

## Development Workflow

### 1. Make Changes

Edit files in `app/`, `components/`, or `lib/`:

```bash
# Example: Edit a component
code components/SeoReceipt.tsx
```

### 2. See Changes Immediately

Next.js dev server has Fast Refresh:
- Component changes update instantly
- No full page reload
- State preserved when possible

### 3. Check Types

```bash
npm run typecheck
```

Fix any TypeScript errors before committing.

### 4. Test Manually

Navigate to the changed page:
- Click demo chips to test parameters
- View SEO Receipt to verify logic
- Test on mobile viewport

### 5. Build Before Committing

```bash
npm run build
```

Ensures the production build works.

## Common Development Tasks

### Adding a New Page

1. Create file in `app/`:
   ```tsx
   // app/my-page/page.tsx
   export default function MyPage() {
     return <div>My new page</div>;
   }
   ```

2. Visit http://localhost:3000/my-page

3. SEO Receipt automatically works on your page

### Adding a New Component

1. Create file in `components/`:
   ```tsx
   // components/MyComponent.tsx
   'use client';  // If using hooks

   export function MyComponent() {
     return <div>My component</div>;
   }
   ```

2. Import and use:
   ```tsx
   import { MyComponent } from '@/components/MyComponent';
   ```

### Modifying SEO Logic

1. Edit `lib/rules/canonical.ts`

2. Check trace in SEO Receipt to verify

3. Update tests if needed

### Adding a New Parameter Rule

1. Edit `data/rules.json`:
   ```json
   {
     "name": "my_param",
     "policy": "stable",
     "description": "My parameter description"
   }
   ```

2. Restart dev server to pick up changes

3. Rule is automatically applied

### Styling with Tailwind

Use Tailwind classes directly:
```tsx
<div className="flex items-center gap-4 p-4 bg-blue-50 rounded-lg">
  Content
</div>
```

VS Code IntelliSense shows available classes.

## Testing Your Changes

### Manual Testing Checklist

- [ ] Visit homepage
- [ ] Click through catalog pages
- [ ] Test parameter combinations with chips
- [ ] Check SEO Receipt updates
- [ ] Test on mobile viewport
- [ ] Verify robots.txt at /api/technical-seo/robots
- [ ] Verify sitemap at /api/technical-seo/sitemap
- [ ] Test best practices sections
- [ ] Run `npm run build` successfully

### Browser Testing

Test in multiple browsers:
- Chrome/Edge (Chromium)
- Firefox
- Safari (if on Mac)

