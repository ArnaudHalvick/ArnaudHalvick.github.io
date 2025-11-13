# Installation

This guide will help you set up your development environment and start working with the SEO Best Practices Showcase.

## Prerequisites

### Required Software

1. **Node.js** (v18 or higher)
   ```bash
   node --version  # Should be v18.0.0 or higher
   ```

2. **npm** (comes with Node.js)
   ```bash
   npm --version  # Should be 9.0.0 or higher
   ```

3. **Git** (for cloning the repository)
   ```bash
   git --version
   ```

### Recommended Tools

- **VS Code** - IDE with excellent TypeScript support
- **VS Code Extensions**:
  - ESLint
  - Prettier
  - Tailwind CSS IntelliSense
  - TypeScript and JavaScript Language Features

## Installation Steps

### Step 1: Clone the Repository

```bash
git clone <repository-url>
cd project
```

### Step 2: Install Dependencies

```bash
npm install
```

This will install all dependencies listed in `package.json`, including:
- Next.js
- React
- TypeScript
- Tailwind CSS
- shadcn/ui components
- Lucide icons

### Step 3: Verify Installation

```bash
npm run typecheck
```

This should complete without errors. If you see TypeScript errors, verify your Node.js version and try deleting `node_modules` and running `npm install` again.

