# Documentation Index

Welcome to the SEO Workshop technical documentation. This folder contains comprehensive guides for understanding, developing, and extending the project.

## Quick Start

### Installation

```bash
# Clone the repository
git clone <repository-url>
cd seo-playground

# Install dependencies
npm install

# Run development server
npm run dev
```

Visit `http://localhost:3000` to explore.

### Tech Stack

- **Next.js 15** with App Router
- **React 18** and TypeScript 5
- **Tailwind CSS 3** and shadcn/ui components
- **Lucide React** for icons
- Framework-agnostic SEO logic in `/lib/rules/`

The SEO decision engine is intentionally decoupled from the framework, making it portable to any stack.

## Documentation Structure

### 📚 Getting Started

1. **[Main README](../../README.md)** - Start here

   - Project overview
   - Quick start guide
   - Tech stack summary
   - Key features

2. **[Setup Guide](./setup/installation.md)** - Development environment
   - [Installation](./setup/installation.md) - Prerequisites and installation steps
   - [Development](./setup/development.md) - Dev workflow and common tasks
   - [Configuration](./setup/configuration.md) - Project structure and config files
   - [Troubleshooting](./setup/troubleshooting.md) - Debugging and common issues

### 🏗️ Architecture & Design

3. **[Architecture](./architecture/)** - System design

   - [Overview](./architecture/overview.md) - System overview and architecture layers
   - [Data Flow](./architecture/data-flow.md) - Data flow and component interactions
   - [Design Patterns](./architecture/patterns.md) - Key design patterns and state management
   - [Routing](./architecture/routing.md) - Clean path routing strategy and multi-select detection
   - [Filters](./architecture/filters.md) - Filter system architecture and size configuration
   - [Performance](./architecture/performance.md) - Performance considerations and build/runtime
   - [Deployment](./architecture/deployment.md) - Deployment, extensibility, and testing

4. **[SEO Logic](./seo-logic/)** - Decision algorithms
   - [Overview](./seo-logic/overview.md) - Decision flow introduction
   - [Decision Steps](./seo-logic/decision-steps.md) - Detailed explanation of all 10 steps
   - [Parameters](./seo-logic/parameters.md) - Parameter policy deep dive and stacked parameters
   - [Multi-Select](./seo-logic/multi-select.md) - Multi-select parameters deep dive
   - [Canonical Strategy](./seo-logic/canonical.md) - Canonical vs robots.txt, best practices, debugging

### 🎨 Implementation Details

5. **[Components](./components/)** - UI components

   - [Overview](./components/overview.md) - Component hierarchy and introduction
   - [SEO Receipt](./components/seo-receipt.md) - Real-time SEO feedback component
   - [Filters](./components/filters.md) - Filter components (FilterSidebar, FilterSummaryBar, etc.)
   - [Catalog](./components/catalog.md) - Catalog pages and clean path components
   - [Playground](./components/playground.md) - Pattern gallery and best practices components
   - [UI Library](./components/ui-library.md) - shadcn/ui components and utilities
   - [Patterns](./components/patterns.md) - Component patterns, testing, and best practices

6. **[Strategic SEO](./strategic-seo/)** - Strategic SEO section

   - [Overview](./strategic-seo/overview.md) - Section overview and structure
   - [Navigation](./strategic-seo/navigation.md) - Navigation structure and dropdown menus
   - [Pages](./strategic-seo/pages.md) - Brief descriptions of each page

7. **[Extending](./extending/)** - Adding features
   - [Overview](./extending/overview.md) - Introduction and table of contents
   - [Parameters](./extending/parameters.md) - Adding parameter rules and robots.txt patterns
   - [Filters](./extending/filters.md) - Adding filter types, size groups, and gender filters
   - [Routing](./extending/routing.md) - Creating clean path routes
   - [SEO Logic](./extending/seo-logic.md) - Modifying SEO logic and crawl trap detection
   - [Pages & Components](./extending/pages-components.md) - Adding pages, components, and UI customization
   - [Advanced](./extending/advanced.md) - Advanced extensions, contributing guidelines, and testing

## Quick Navigation

### For Developers

**First time here?**

1. Read [Main README](../../README.md)
2. Follow [Setup Guide](./setup/installation.md)
3. Review [Architecture Overview](./architecture/overview.md)
4. Start coding!

**Adding a feature?**

1. Check [Extending Overview](./extending/overview.md)
2. Review [SEO Logic](./seo-logic/overview.md) if modifying rules
3. Check [Components](./components/overview.md) for UI patterns

**Debugging SEO decisions?**

1. Read [SEO Logic Overview](./seo-logic/overview.md)
2. Check [Decision Steps](./seo-logic/decision-steps.md) for detailed flow
3. Use the SEO Receipt's "Rule Trace" tab
4. Review [Canonical Strategy](./seo-logic/canonical.md) for debugging tips

### For AI/LLMs

**Understanding the codebase:**

- [Architecture Overview](./architecture/overview.md) - System structure and patterns
- [SEO Logic Overview](./seo-logic/overview.md) - Core algorithm documentation
- [Components Overview](./components/overview.md) - UI component details

**Making changes:**

- [Extending Overview](./extending/overview.md) - How to add features
- [Setup](./setup/installation.md) - Environment and tooling

**Key files to understand:**

- `lib/rules/canonical.ts` - Core SEO decision engine
- `lib/rules/params.ts` - Parameter classification
- `components/SeoReceipt.tsx` - Real-time feedback UI
- `data/rules.json` - Configuration
- `app/strategic-seo/` - Strategic SEO pages
- `components/Navigation.tsx` - Navigation with section detection

## Documentation Standards

All documentation follows these principles:

1. **Clear Structure**: Organized with headers and sections
2. **Code Examples**: Practical examples for all concepts
3. **Visual Aids**: Diagrams and tables where helpful
4. **Links**: Cross-references between documents
5. **Up-to-date**: Maintained alongside code changes

## Contributing to Documentation

When adding features:

1. Update relevant documentation files
2. Add code examples
3. Update the main README if needed
4. Keep documentation in sync with code

When writing documentation:

- Use clear, simple language
- Include practical examples
- Add diagrams for complex flows
- Link to related documentation
- Test all code examples

## File Organization

Documentation is organized into topic-based subfolders:

- **architecture/** - System design and architecture details
- **components/** - React component documentation
- **seo-logic/** - SEO decision algorithms
- **strategic-seo/** - Strategic SEO section documentation
- **extending/** - Feature extension guides
- **setup/** - Development setup and configuration

Each subfolder contains focused files (~150-250 lines each) for easier navigation and AI searchability.

## Questions?

- Check the [Setup Guide](./setup/troubleshooting.md) for environment issues
- Check [Architecture](./architecture/overview.md) for system design questions
- Check [SEO Logic](./seo-logic/overview.md) for algorithm questions
- Check [Components](./components/overview.md) for UI questions
- Check [Extending](./extending/overview.md) for contribution questions

## External Resources

### Next.js Documentation

- [Next.js 13 Docs](https://nextjs.org/docs)
- [App Router](https://nextjs.org/docs/app)
- [API Routes](https://nextjs.org/docs/app/building-your-application/routing/route-handlers)

### React Documentation

- [React Docs](https://react.dev/)
- [Hooks Reference](https://react.dev/reference/react)

### TypeScript

- [TypeScript Handbook](https://www.typescriptlang.org/docs/)

### Tailwind CSS

- [Tailwind Docs](https://tailwindcss.com/docs)
- [Utility Classes](https://tailwindcss.com/docs/utility-first)

### shadcn/ui

- [Component Library](https://ui.shadcn.com/)
- [Installation Guide](https://ui.shadcn.com/docs/installation)

## Feedback

Documentation improvements are always welcome! If you find:

- Unclear explanations
- Missing information
- Errors or typos
- Outdated content

Please contribute updates or open an issue.

---

**Last Updated**: November 2025

## Recent Changes

### November 2025 Updates

- **URL Structure Refactor**: Moved all pages under section-specific paths
  - Technical pages: `/technical-seo/*` (previously at root level)
  - Strategic pages: `/strategic-seo/*` (previously `/strategic/*`)
  - Shop kept at root `/shop` as flagship demo
- **Two-Section Architecture**: Clear separation between Technical SEO and Strategic SEO
- **Next.js 15 Upgrade**: Updated from 13.5 to 15 with stricter JSX parsing
- **Interactive Demo**: Renamed from "Shop" to "Interactive Demo" for clarity
- **SEO Dev Tools Page**: New `/technical-seo/seo-dev-tools` page with curated toolkit
- **Enhanced Navigation**: Section-aware navigation with switcher dropdown
- **SEO Receipt**: Conditional rendering only on technical pages
