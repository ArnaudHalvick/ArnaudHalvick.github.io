# Troubleshooting

## Debugging

### TypeScript Errors

```bash
npm run typecheck
```

Shows all type errors with file locations.

### React Errors

Check browser console (F12):
- Red errors show stack traces
- Click file names to see source

### SEO Logic Issues

1. Open SEO Receipt
2. Click "Rule Trace" tab
3. Review step-by-step decisions
4. Look for unexpected behavior

### Build Errors

```bash
npm run build
```

Shows all compilation errors.

## Hot Reload Issues

If hot reload stops working:

1. Save the file again
2. Refresh browser (Cmd+R / Ctrl+R)
3. Restart dev server:
   ```bash
   # Stop with Ctrl+C
   npm run dev
   ```

## Performance Tips

### Dev Server

The dev server is not optimized for performance:
- Slow initial page load (compiling)
- Faster after first load
- Production builds are much faster

### Tailwind CSS

First build is slow (generates all classes):
- Subsequent builds are faster
- Production builds are optimized

## Common Issues

### Port Already in Use

```
Error: Port 3000 is already in use
```

Solution:
```bash
# Kill process on port 3000
lsof -ti:3000 | xargs kill
# Or use a different port
PORT=3001 npm run dev
```

### Module Not Found

```
Error: Cannot find module '@/components/...'
```

Solution:
```bash
# Restart TypeScript server in VS Code
Cmd+Shift+P → "TypeScript: Restart TS Server"

# Or restart dev server
```

### Tailwind Classes Not Applying

Solution:
```bash
# Delete .next folder and rebuild
rm -rf .next
npm run dev
```

### TypeScript Errors After Update

Solution:
```bash
# Rebuild TypeScript
npm run typecheck

# Delete node_modules and reinstall
rm -rf node_modules
npm install
```

## Production Build

### Building

```bash
npm run build
```

Creates optimized production build in `.next/` folder.

### Starting Production Server

```bash
npm start
```

Serves the built application on port 3000.

### Build Output

```
Route (app)                    Size     First Load JS
┌ ○ /                         189 B          86.2 kB
├ ○ /catalog                  1.72 kB        87.7 kB
├ ● /catalog/[category]       5.21 kB        99.5 kB
...
```

Legend:
- `○` Static (generated at build time)
- `●` SSG (static site generation)
- `λ` Server (server-side rendering)

## Next Steps

- Read [Architecture](../architecture/overview.md) to understand the system design
- Read [SEO Logic](../seo-logic/overview.md) to understand decision algorithms
- Read [Components](../components/overview.md) to understand UI components
- Read [Extending](../extending/overview.md) to learn how to add features

## Getting Help

### Check Documentation

1. Start with [README](../../README.md)
2. Review [Architecture](../architecture/overview.md) for system overview
3. Check [SEO Logic](../seo-logic/overview.md) for decision flow
4. Review [Components](../components/overview.md) for UI details

### Debug with SEO Receipt

The SEO Receipt is your best debugging tool:
- Shows complete decision trace
- Explains every step
- Highlights issues

### Check the Code

All business logic is in `lib/rules/`:
- `canonical.ts` - Main decision engine
- `params.ts` - Parameter classification
- `robots.ts` - Robots.txt logic
- `sitemap.ts` - Sitemap generation

Code is well-commented and type-safe.

