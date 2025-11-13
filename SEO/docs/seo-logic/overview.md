# SEO Logic Overview

This document provides a deep dive into the SEO decision-making algorithms used in this application.

## Overview

All SEO decisions flow through the `computeCanonical()` function in `lib/rules/canonical.ts`. This function implements a deterministic, step-by-step decision process that mirrors real-world SEO best practices.

## The Decision Flow

Every URL goes through exactly 10 steps in order:

```
1. Normalize Path
2. Classify Parameters
3. Detect Pagination
4. Check Protected Routes
5. Check robots.txt Blocking
6. Detect Multi-Select Parameters
7. Apply Multi-Filter Logic
8. Apply Parameter Policies
9. Build Canonical URL
10. Determine Sitemap Inclusion
```

Each step is explained in detail in the [Decision Steps](./decision-steps.md) document.

