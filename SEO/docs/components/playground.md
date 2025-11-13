# Playground Components

Components used in the SEO pattern gallery and best practices showcase.

## ParamPolicyEditor

**Location**: `components/playground/ParamPolicyEditor.tsx`

**Purpose**: Display parameter classification rules

**Type**: Client Component

**Props**:
- `config: ParamConfig` - SEO configuration

**Features**:
1. **Grouped by Policy**
   - Stable parameters group
   - Unstable parameters group
   - Blocked parameters group

2. **Badge Display**
   - Color-coded badges (green/amber/red)
   - Policy explanations

3. **Static Display**
   - No toggles (best practices only)
   - Educational descriptions

**Code Structure**:
```typescript
export function ParamPolicyEditor({ config }: ParamPolicyEditorProps) {
  const groupedRules = {
    stable: config.rules.filter(r => r.policy === 'stable'),
    unstable: config.rules.filter(r => r.policy === 'unstable'),
    blocked: config.rules.filter(r => r.policy === 'blocked'),
  };

  return (
    <Card>
      {['stable', 'unstable', 'blocked'].map(policyType => (
        <div key={policyType}>
          <Badge>{policyType}</Badge>
          <p>{getPolicyExplanation(policyType)}</p>
          {groupedRules[policyType].map(rule => (
            <div key={rule.name}>
              <code>{rule.name}</code>
              <p>{rule.description}</p>
            </div>
          ))}
        </div>
      ))}
    </Card>
  );
}
```

## PaginationSettings

**Location**: `components/playground/PaginationSettings.tsx`

**Purpose**: Display pagination best practices

**Type**: Client Component

**Props**: None (static display)

**Features**:
1. **Page 1 Explanation**
   - index,follow
   - Why it's indexable
   - Code example

2. **Page 2+ Explanation**
   - noindex,follow
   - Why this is recommended
   - Benefits listed

3. **Canonical Strategy**
   - Self-referencing canonical
   - Why not canonical to page 1
   - Code examples

4. **Anti-Patterns**
   - Blocking pagination in robots.txt
   - Why it's bad
   - Warnings

## RobotsPreview

**Location**: `components/playground/RobotsPreview.tsx`

**Purpose**: Display live-generated robots.txt with section-by-section annotations

**Type**: Client Component

**Props**: None (uses `generateRobotsTxt()` internally)

**Features**:
1. **Live Generation**
   - Calls `generateRobotsTxt(DEFAULT_PARAM_CONFIG)`
   - Shows actual `/api/technical-seo/robots` output
   - Always in sync with robots.ts logic
   - No hardcoded examples

2. **Automatic Parsing**
   - Splits output into sections by comment headers
   - Extracts Clean Path Architecture, Protected Paths, Tracking Parameters, etc.
   - Maps each section to explanation and icon

3. **Section Annotations**
   - Each section gets: icon, title, badge, explanation, code block
   - Badge types: Critical (green), Recommended (light green), Info (blue)
   - Colored badges indicate importance level

4. **Complete File View**
   - Shows full robots.txt in terminal-style code block
   - Note indicating it matches `/api/technical-seo/robots`

5. **Anti-Patterns Section**
   - Educational warnings about common mistakes
   - Blocking pagination, sort, search pages
   - Explains why each is problematic

**Key Improvement**: Now dynamically generates content from `robots.ts` instead of hardcoded examples, ensuring documentation never drifts from implementation.

## RobotsTester

**Location**: `components/playground/RobotsTester.tsx`

**Purpose**: Interactive URL testing tool to demonstrate robots.txt pattern matching

**Type**: Client Component

**Props**: None (uses `checkRobotsBlocking()` internally)

**Features**:
1. **URL Input**
   - Text input for custom URLs
   - Enter key support for quick testing
   - Real-time validation

2. **Pattern Matching**
   - Calls `checkRobotsBlocking()` with pathname and search params
   - Shows whether URL is blocked or allowed
   - Lists all matched rules with explanations

3. **Visual Feedback**
   - Green alert for allowed URLs
   - Red alert for blocked URLs
   - Icons (CheckCircle2 / XCircle)
   - Matched rules displayed in monospace font

4. **Example URLs**
   - 6 pre-configured examples with descriptions
   - Click to test instantly
   - Shows expected result

## SitemapTable

**Location**: `components/playground/SitemapTable.tsx`

**Purpose**: Show which URLs are in the sitemap

**Type**: Client Component

**Props**:
- `config: ParamConfig` - SEO configuration

**Features**:
1. **Entry List**
   - All evaluated URLs
   - Inclusion status
   - Reasons for inclusion/exclusion

2. **Filtering**
   - Show all entries
   - Show included only
   - Show excluded only

3. **Explanations**
   - Why each URL is included/excluded
   - Links to relevant documentation

## PatternCard

**Location**: `components/playground/PatternCard.tsx`

**Purpose**: Display individual SEO pattern with full details, examples, and SEO impact

**Type**: Client Component

**Props**:
```typescript
interface PatternCardProps {
  pattern: SeoPattern;
}

interface SeoPattern {
  id: string;
  name: string;
  icon: string;
  category: string;
  risk: 'high' | 'medium' | 'low' | 'varies' | 'none';
  description: string;
  explanation: string;
  example: { url: string; liveLink: string };
  whenToUse: string[];
  whenToAvoid: string[];
  strategy: string;
  implementation: string;
  seoImpact: {
    indexable: boolean | null;
    robotsBlocked: boolean | null;
    sitemapIncluded: boolean | null;
  };
}
```

**Features**:
1. **Visual Risk Indicators**
   - Color-coded card borders based on risk level
   - Risk badge (High/Medium/Low/Varies/N/A)
   - Consistent color scheme: red=high, orange=medium, green=low

2. **Pattern Information**
   - Icon and pattern name
   - Short description and detailed explanation
   - Real URL example with syntax highlighting
   - "Try it Live" button linking to shop

3. **Usage Guidance**
   - "When to Use" list with green checkmarks
   - "When to Avoid" list with red X marks
   - Implementation strategy explanation

4. **SEO Impact Display**
   - Three-column grid showing:
     - Indexable (Yes/No/Varies)
     - Robots (Blocked/Allowed/Varies)
     - Sitemap (Yes/No/Varies)
   - Visual indicators with color coding

## PatternFilter

**Location**: `components/playground/PatternFilter.tsx`

**Purpose**: Filter and search SEO patterns by risk level, category, and keywords

**Type**: Client Component

**Props**:
```typescript
interface PatternFilterProps {
  searchQuery: string;
  onSearchChange: (value: string) => void;
  selectedRisk: string | null;
  onRiskChange: (risk: string | null) => void;
  selectedCategory: string | null;
  onCategoryChange: (category: string | null) => void;
  activeFilterCount: number;
  onClearAll: () => void;
}
```

**Features**:
1. **Search Bar**
   - Full-text search across pattern names, descriptions
   - Search icon with clear button
   - Real-time filtering

2. **Risk Level Filter**
   - Buttons for: All, Low, Medium, High, Varies
   - Color-coded buttons matching pattern cards
   - Single selection mode

3. **Category Filter**
   - Buttons for: All, Filtering, Sorting, Ranges, URL Strategies, Navigation, Access Control
   - Blue highlight for selected category
   - Single selection mode

4. **Active Filter Summary**
   - Badge showing number of active filters
   - "Clear All" button to reset all filters
   - Only shown when filters are active

## PatternCategory

**Location**: `components/playground/PatternCategory.tsx`

**Purpose**: Collapsible section grouping patterns by category with visual header

**Type**: Client Component

**Props**:
```typescript
interface PatternCategoryProps {
  title: string;
  description: string;
  icon: string;
  patterns: SeoPattern[];
  defaultExpanded?: boolean;
}
```

**Features**:
1. **Collapsible Section**
   - Click to expand/collapse category
   - Chevron icon indicates state
   - Smooth animation on expand

2. **Category Header**
   - Large icon representing category
   - Title and description
   - Pattern count badge
   - Gradient background with hover effect

3. **Pattern Grid**
   - Responsive grid layout (1/2/3 columns)
   - Contains PatternCard components
   - Only renders when expanded

4. **Empty State Handling**
   - Returns null if no patterns in category
   - Automatically hides empty categories

**Category Integration**:
The playground page uses multiple PatternCategory components organized in a specific order:
1. Filtering Patterns
2. Sorting & Layout
3. Range Filters
4. URL Strategies
5. Navigation Patterns
6. Access Control

---

## SEO Dev Tools Page (NEW - November 2025)

**Location**: `app/technical-seo/seo-dev-tools/page.tsx`

**Purpose**: Curated reference guide for technical SEO tools

**Type**: Server Component (static page)

**Features**:
1. **Tool Categories**
   - Crawl & Analysis: Screaming Frog, Sitebulb, JetOctopus
   - Performance & Core Web Vitals: Lighthouse, WebPageTest, PageSpeed Insights
   - Rendering & Testing: Puppeteer, Playwright, Chrome DevTools
   - Deploy & Monitor: Vercel, Netlify, GitHub Actions, Cloudflare
   - Security & Headers: SecurityHeaders.com, curl/HTTPie

2. **Starter Stack Section**
   - Alert box with green gradient
   - Recommended tools for small teams
   - High-impact, budget-friendly options

3. **Migration Checklist**
   - Essential steps for site migrations
   - Orange border to highlight importance
   - Checklist format with checkmark icons

4. **Best Practices**
   - Tips for tool organization
   - Process recommendations
   - Badge-styled tips

**Design**:
- Simple list-based layout for easy scanning
- Each tool has bold name + brief description
- Sections use icon headers for visual organization
- Responsive layout with right padding for SEO Receipt

**Navigation**:
- Added to top nav under "Tools & Resources"
- Card on homepage in "Tools & Resources" section
- Wrench icon for consistency

