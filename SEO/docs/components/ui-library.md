# UI Component Library

## Utility Components

### Breadcrumbs

**Location**: `components/Breadcrumbs.tsx`

**Purpose**: Show navigation breadcrumb trail

**Type**: Client Component

**Props**:
- `items: Array<{ label: string, href: string }>`

**Features**:
- Home link always present
- Current page non-clickable
- Separator between items

### Navigation

**Location**: `components/Navigation.tsx`

**Purpose**: Main site navigation

**Type**: Client Component

**Features**:
- Links to all main sections
- Responsive mobile menu
- Active page highlighting

## shadcn/ui Components

**Location**: `components/ui/`

These are re-usable components from shadcn/ui, built on Radix UI primitives.

Key components used:

- **`button.tsx`** - Buttons with variants
- **`card.tsx`** - Card containers
- **`badge.tsx`** - Status badges
- **`tabs.tsx`** - Tab navigation
- **`alert.tsx`** - Alert messages
- **`table.tsx`** - Data tables
- **`dialog.tsx`** - Modal dialogs
- **`tooltip.tsx`** - Tooltips

All use Tailwind CSS for styling and include proper TypeScript types.

## Hooks

### useToast

**Location**: `hooks/use-toast.ts`

**Purpose**: Toast notification system for user feedback

**Type**: Custom React hook

**Usage**:
```typescript
import { useToast, toast } from '@/hooks/use-toast';

function MyComponent() {
  const { toast: toastFn } = useToast();
  
  const handleClick = () => {
    toastFn({
      title: "Success",
      description: "Operation completed successfully",
    });
  };
  
  // Or use the standalone toast function
  toast({
    title: "Error",
    description: "Something went wrong",
    variant: "destructive",
  });
}
```

**API**:

#### `useToast()` Hook
Returns an object with:
- `toast`: Function to show a toast notification
- `dismiss`: Function to dismiss a toast by ID
- `toasts`: Array of current toast notifications

#### `toast()` Function (standalone)
Direct function to show a toast without using the hook.

**Toast Options**:
- `title?`: React.ReactNode - Toast title
- `description?`: React.ReactNode - Toast description
- `variant?`: "default" | "destructive" - Toast variant
- `action?`: ToastActionElement - Optional action button

**Features**:
- Singleton pattern - one toast at a time (TOAST_LIMIT = 1)
- Auto-dismiss after delay (TOAST_REMOVE_DELAY)
- React Context-based state management
- Used by shadcn/ui toast components (`components/ui/toast.tsx`, `components/ui/toaster.tsx`)

**Note**: This hook is inspired by react-hot-toast and provides the underlying state management for the shadcn/ui toast component system.

