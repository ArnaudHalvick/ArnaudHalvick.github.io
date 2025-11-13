# Design Patterns

## Key Design Patterns

### 1. Single Responsibility Principle

Each module has one clear responsibility:
- `canonical.ts` decides SEO outcomes
- `params.ts` classifies parameters
- `robots.ts` handles robots.txt
- `sitemap.ts` generates sitemaps

### 2. Separation of Concerns

Business logic is **completely separated** from presentation:
- SEO logic in `lib/` has zero React dependencies
- Can be tested in isolation
- Can be reused in other frameworks

### 3. Immutable Data Flow

Configuration is treated as immutable:
- Config is loaded once at startup
- Updates create new config objects
- No direct mutations

### 4. Declarative Configuration

SEO rules are defined declaratively in JSON:
```json
{
  "name": "sort",
  "policy": "unstable",
  "description": "..."
}
```

## State Management

### Global State (Configuration)

Managed via **React Context**:
- Single source of truth
- Available to all components
- Persisted to localStorage (demo only)

### Component State

Managed via **useState**:
- UI interactions (tabs, tooltips, etc.)
- Temporary state (clipboard copy status)
- No shared state between components

### URL State

Managed via **Next.js Router**:
- URL parameters drive SEO decisions
- No client-side routing needed
- Server-side rendering for SEO

