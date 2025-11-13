# Multi-Select Parameters

## What is Multi-Select?

Multi-select occurs when users select multiple values for a single filter type. In URLs, this typically appears as comma-separated values:

```
?color=black,blue       # 2 colors selected
?color=black,blue,red   # 3 colors selected
?size=s,m,l             # 3 sizes selected
```

## Why is This Problematic?

Multi-select creates **exponential URL combinations** (2^N):

```
User selects: [black, blue]

Possible URLs:
1. /catalog/t-shirts                   (neither selected)
2. /catalog/t-shirts?color=black       (just black)
3. /catalog/t-shirts?color=blue        (just blue)
4. /catalog/t-shirts?color=black,blue  (both selected)

Total: 2^2 = 4 URLs
```

```
User selects: [black, blue, red]

Possible URLs:
1. /catalog/t-shirts                         (none)
2. /catalog/t-shirts?color=black             (just black)
3. /catalog/t-shirts?color=blue              (just blue)
4. /catalog/t-shirts?color=red               (just red)
5. /catalog/t-shirts?color=black,blue        (black+blue)
6. /catalog/t-shirts?color=black,red         (black+red)
7. /catalog/t-shirts?color=blue,red          (blue+red)
8. /catalog/t-shirts?color=black,blue,red    (all three)

Total: 2^3 = 8 URLs
```

**Real-World Impact**:
- 5 colors: 2^5 = 32 URLs
- 10 colors: 2^10 = 1,024 URLs
- 15 colors: 2^15 = 32,768 URLs

## SEO Strategy for Multi-Select

**Use robots.txt blocking** (NOT noindex,follow):

```
Disallow: /*?*color=*,*
Disallow: /*?*size=*,*
```

**Why robots.txt?**
- ✅ Prevents crawlers from discovering exponential combinations
- ✅ Saves massive crawl budget
- ✅ Scales to any number of options
- ❌ noindex,follow would still allow crawling (wasteful)

**Implementation**:
1. Detect comma in parameter value: `value.includes(',')`
2. Set `blockInRobots = true`
3. Set `robots = undefined` (no meta tag needed)
4. Set `sitemapIncluded = false`
5. Add blocking pattern to robots.txt

## Multi-Select vs Single-Select

| Aspect | Single-Select | Multi-Select |
|--------|---------------|--------------|
| URL | `?color=black` | `?color=black,blue` |
| Combinations | N (linear) | 2^N (exponential) |
| SEO Strategy | index,follow | robots.txt block |
| User Value | High | Medium (niche use case) |
| Crawl Cost | Low | Catastrophic |

