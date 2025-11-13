# Strategic SEO Navigation

This document explains how navigation works in the Strategic SEO section.

## Navigation Structure

The Strategic SEO section has its own navigation dropdown menus defined in `components/Navigation.tsx`. The navigation automatically detects when users are in the Strategic SEO section and displays the appropriate menus.

## Dropdown Menus

The Strategic SEO section has three dropdown menus:

### 1. Strategy & Planning
- **Research & Strategy** → `/strategic-seo/research-strategy`
- **Content Architecture** → `/strategic-seo/content-architecture`

### 2. Production & Growth
- **Content Production** → `/strategic-seo/content-production`
- **Measurement & Optimization** → `/strategic-seo/measurement-optimization`
- **Authority Building** → `/strategic-seo/authority-building`

### 3. Tools & Resources
- **Strategic Tools** → `/strategic-seo/strategic-tools`
- **Resources** → `/strategic-seo/resources`

## Navigation Component

The navigation is defined in `components/Navigation.tsx` using the `strategicCategories` array:

```typescript
const strategicCategories = [
  {
    name: "Strategy & Planning",
    items: [
      { name: "Research & Strategy", path: "/strategic-seo/research-strategy", icon: Target },
      { name: "Content Architecture", path: "/strategic-seo/content-architecture", icon: FileText },
    ],
  },
  {
    name: "Production & Growth",
    items: [
      { name: "Content Production", path: "/strategic-seo/content-production", icon: Users },
      { name: "Measurement & Optimization", path: "/strategic-seo/measurement-optimization", icon: BarChart3 },
      { name: "Authority Building", path: "/strategic-seo/authority-building", icon: TrendingUp },
    ],
  },
  {
    name: "Tools & Resources",
    items: [
      { name: "Strategic Tools", path: "/strategic-seo/strategic-tools", icon: Search },
      { name: "Resources", path: "/strategic-seo/resources", icon: FileText },
    ],
  },
];
```

## Section Detection

The navigation component automatically detects which section the user is in:

```typescript
const isStrategicSection = pathname.startsWith("/strategic-seo");
const isTechnicalSection = pathname.startsWith("/technical-seo") || 
  (!pathname.startsWith("/strategic-seo") && pathname !== "/");
```

When `isStrategicSection` is true, the `strategicCategories` are used instead of `technicalCategories`.

## Section Switcher

A prominent "Switch to Technical SEO" button appears in the header when users are in the Strategic SEO section. This allows easy navigation between the two main sections of the site.

## Active State

The navigation highlights active categories and items:

- **Category buttons** are highlighted when any item in that category is active
- **Menu items** are highlighted when their path matches the current pathname

## Mobile Navigation

On mobile devices, the navigation collapses into a hamburger menu (Sheet component) that shows:
- Section switcher button
- All dropdown categories as collapsible sections
- Direct links to all pages

## Adding Items to Navigation

To add a new page to the navigation:

1. Add the page to the appropriate category in `strategicCategories`
2. Include the path, name, and icon
3. The navigation will automatically include it in the dropdown

Example:

```typescript
{
  name: "Production & Growth",
  items: [
    // ... existing items
    { name: "New Page", path: "/strategic-seo/new-page", icon: NewIcon },
  ],
}
```

## Icons

All navigation icons come from `lucide-react`. Common icons used:
- `Target` - Research & Strategy
- `FileText` - Content Architecture, Resources
- `Users` - Content Production
- `BarChart3` - Measurement & Optimization
- `TrendingUp` - Authority Building
- `Search` - Strategic Tools

## Related Documentation

- [Overview](./overview.md) - Strategic SEO section overview
- [Pages](./pages.md) - Page descriptions
- [Components Overview](../components/overview.md) - Navigation component details

