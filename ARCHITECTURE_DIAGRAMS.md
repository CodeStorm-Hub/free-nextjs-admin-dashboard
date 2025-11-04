# TailAdmin Architecture Diagrams

**Project:** TailAdmin Next.js Dashboard  
**Version:** 2.0.2

---

## Application Architecture Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                         Browser / Client                            │
└─────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     Next.js 15 App Router                           │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    Root Layout (layout.tsx)                   │ │
│  │  ┌────────────────────────────────────────────────────────┐  │ │
│  │  │  ThemeProvider (Dark/Light Mode)                       │  │ │
│  │  │    ┌────────────────────────────────────────────────┐  │  │ │
│  │  │    │  SidebarProvider (Sidebar State)              │  │  │ │
│  │  │    │    ┌────────────────────────────────────────┐ │  │  │ │
│  │  │    │    │  Route Groups                         │ │  │  │ │
│  │  │    │    │                                        │ │  │  │ │
│  │  │    │    │  ┌──────────────┐  ┌───────────────┐  │ │  │  │ │
│  │  │    │    │  │ (admin)      │  │ (full-width)  │  │ │  │  │ │
│  │  │    │    │  │  - Dashboard │  │  - Auth       │  │ │  │  │ │
│  │  │    │    │  │  - Calendar  │  │  - Error 404  │  │ │  │  │ │
│  │  │    │    │  │  - Profile   │  │               │  │ │  │  │ │
│  │  │    │    │  │  - Forms     │  │               │  │ │  │  │ │
│  │  │    │    │  │  - Tables    │  │               │  │ │  │  │ │
│  │  │    │    │  │  - Charts    │  │               │  │ │  │  │ │
│  │  │    │    │  │  - UI Elems  │  │               │  │ │  │  │ │
│  │  │    │    │  └──────────────┘  └───────────────┘  │ │  │  │ │
│  │  │    │    └────────────────────────────────────────┘ │  │  │ │
│  │  │    └────────────────────────────────────────────────┘  │  │ │
│  │  └────────────────────────────────────────────────────────┘  │ │
│  └──────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Component Hierarchy

```
Root Layout
│
├─ ThemeProvider
│  └─ Manages: theme state, localStorage, dark class
│
├─ SidebarProvider
│  └─ Manages: sidebar state, mobile state, hover state
│
└─ Route Group Layouts
   │
   ├─ Admin Layout (admin routes)
   │  │
   │  ├─ AppSidebar
   │  │  ├─ Logo
   │  │  ├─ Navigation Menu
   │  │  │  ├─ Main Section
   │  │  │  │  ├─ Dashboard (with submenu)
   │  │  │  │  ├─ Calendar
   │  │  │  │  ├─ Profile
   │  │  │  │  ├─ Forms (with submenu)
   │  │  │  │  ├─ Tables (with submenu)
   │  │  │  │  └─ Pages (with submenu)
   │  │  │  │
   │  │  │  └─ Others Section
   │  │  │     ├─ Charts (with submenu)
   │  │  │     ├─ UI Elements (with submenu)
   │  │  │     └─ Authentication (with submenu)
   │  │  │
   │  │  └─ SidebarWidget
   │  │
   │  ├─ Backdrop (mobile overlay)
   │  │
   │  ├─ AppHeader
   │  │  ├─ Sidebar Toggle
   │  │  ├─ Logo (mobile)
   │  │  ├─ Search Bar
   │  │  ├─ ThemeToggleButton
   │  │  ├─ NotificationDropdown
   │  │  └─ UserDropdown
   │  │
   │  └─ Page Content
   │     └─ {children}
   │
   └─ Full-Width Layout (auth/error routes)
      └─ Page Content
         └─ {children}
```

---

## Data Flow Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         User Interaction                        │
└─────────────────────────────────────────────────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Component Layer                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   UI Comps   │  │  Form Comps  │  │  Dashboard   │          │
│  │  - Button    │  │  - Input     │  │  - Metrics   │          │
│  │  - Alert     │  │  - Select    │  │  - Charts    │          │
│  │  - Badge     │  │  - Checkbox  │  │  - Tables    │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Context Layer                              │
│  ┌──────────────────┐         ┌──────────────────┐             │
│  │  ThemeContext    │         │  SidebarContext  │             │
│  │  - theme state   │         │  - expand state  │             │
│  │  - toggleTheme() │         │  - mobile state  │             │
│  │  - localStorage  │         │  - toggles()     │             │
│  └──────────────────┘         └──────────────────┘             │
└─────────────────────────────────────────────────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      State Updates                              │
│  - Re-render affected components                               │
│  - Update CSS classes (dark mode, sidebar width)               │
│  - Persist to localStorage (theme)                             │
└─────────────────────────────────────────────────────────────────┘
```

---

## Sidebar State Machine

```
                    Desktop (>= 1024px)
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
   Collapsed          Expanded            Hovered
   (90px)             (290px)             (290px)
        │                  │                  │
        │  Click Toggle    │  Click Toggle    │
        └─────────►────────┴────────◄─────────┘
        │                                     │
        │  Mouse Enter                        │
        └─────────────────►───────────────────┘
        │                                     │
        │  Mouse Leave                        │
        └─────────────────◄───────────────────┘


                    Mobile (< 1024px)
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
     Hidden            Slide-in           Backdrop
  (off-screen)         (290px)            (visible)
        │                  │                  │
        │  Click Toggle    │  Click Toggle    │
        └─────────►────────┴────────◄─────────┘
        │                                     │
        │  Click Backdrop                     │
        └─────────────────◄───────────────────┘
```

---

## Styling System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      Tailwind CSS v4                            │
└─────────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
┌──────────────┐      ┌──────────────┐     ┌──────────────┐
│   @theme     │      │   @utility   │     │   @layer     │
│  Variables   │      │   Classes    │     │   Overrides  │
└──────────────┘      └──────────────┘     └──────────────┘
        │                     │                     │
        ├─ Colors             ├─ menu-item         ├─ base
        ├─ Typography         ├─ menu-item-active  ├─ components
        ├─ Breakpoints        ├─ no-scrollbar      └─ utilities
        ├─ Shadows            └─ custom-scrollbar
        ├─ Z-index
        └─ Spacing


┌─────────────────────────────────────────────────────────────────┐
│                    CSS Custom Properties                        │
│                                                                 │
│  Base Variables → Tailwind Utilities → Component Classes       │
│                                                                 │
│  --color-brand-500 → bg-brand-500 → menu-item-active          │
└─────────────────────────────────────────────────────────────────┘
```

---

## Theme System Flow

```
User Clicks Theme Toggle
         │
         ▼
ThemeContext.toggleTheme()
         │
         ├─ Update state: theme = "dark" | "light"
         │
         ├─ localStorage.setItem("theme", newTheme)
         │
         └─ Update DOM: document.documentElement.classList
                  │
                  ├─ Add "dark" class    → Dark mode activated
                  └─ Remove "dark" class → Light mode activated
                           │
                           ▼
                  CSS applies dark: variants
                           │
                           ├─ bg-white dark:bg-gray-900
                           ├─ text-gray-700 dark:text-gray-300
                           └─ border-gray-200 dark:border-gray-800
```

---

## Component Communication Patterns

### 1. Context Pattern (Global State)

```
┌──────────────────┐
│  ThemeContext    │
│  ┌────────────┐  │
│  │ Provider   │  │
│  └────────────┘  │
└──────────────────┘
         │
    ┌────┴────┬────────┬────────┐
    ▼         ▼        ▼        ▼
┌─────┐   ┌─────┐  ┌─────┐  ┌─────┐
│Btn A│   │Btn B│  │Hdr  │  │Sdbar│
└─────┘   └─────┘  └─────┘  └─────┘
  uses      uses     uses     uses
useTheme() useTheme() useTheme() useTheme()
```

### 2. Props Pattern (Parent-Child)

```
┌─────────────────┐
│  Parent Comp    │
│  state = {...}  │
└─────────────────┘
         │
    ┌────┴────┬────────┬────────┐
    │         │        │        │
    ▼         ▼        ▼        ▼
┌─────┐   ┌─────┐  ┌─────┐  ┌─────┐
│Child│   │Child│  │Child│  │Child│
│ A   │   │ B   │  │ C   │  │ D   │
└─────┘   └─────┘  └─────┘  └─────┘
props={...} props={...}
```

### 3. Callback Pattern (Child-Parent)

```
┌─────────────────────┐
│  Parent Component   │
│  handleClick(data)  │◄────┐
└─────────────────────┘     │
         │                  │
         ▼                  │
┌─────────────────────┐     │
│  Child Component    │     │
│  onClick={          │     │
│    () => props.     │     │
│    onAction(data)   │─────┘
│  }                  │
└─────────────────────┘
```

---

## Form Component Pattern

```
┌────────────────────────────────────────────┐
│             Form Container                 │
│  ┌──────────────────────────────────────┐ │
│  │  Label Component                     │ │
│  └──────────────────────────────────────┘ │
│  ┌──────────────────────────────────────┐ │
│  │  Input Field                         │ │
│  │  - Prefix Icon (optional)            │ │
│  │  - Input Element                     │ │
│  │  - Suffix Icon (optional)            │ │
│  └──────────────────────────────────────┘ │
│  ┌──────────────────────────────────────┐ │
│  │  Helper Text / Error Message         │ │
│  └──────────────────────────────────────┘ │
└────────────────────────────────────────────┘

States:
├─ Default
├─ Focus (ring + border color change)
├─ Error (red border + error message)
├─ Disabled (opacity + cursor)
└─ Success (green border)
```

---

## Dashboard Page Layout Grid

```
┌───────────────────────────────────────────────────────────────┐
│                        Dashboard Page                         │
│  Grid: 12 columns, gap-4 md:gap-6                            │
│                                                               │
│  ┌─────────────────────────────┐  ┌────────────────────┐     │
│  │  EcommerceMetrics           │  │  MonthlyTarget     │     │
│  │  col-span-12 xl:col-span-7  │  │  col-span-12      │     │
│  │                             │  │  xl:col-span-5    │     │
│  └─────────────────────────────┘  └────────────────────┘     │
│                                                               │
│  ┌─────────────────────────────┐                             │
│  │  MonthlySalesChart          │                             │
│  │  col-span-12 xl:col-span-7  │                             │
│  └─────────────────────────────┘                             │
│                                                               │
│  ┌───────────────────────────────────────────────────────┐   │
│  │  StatisticsChart                                      │   │
│  │  col-span-12                                          │   │
│  └───────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌────────────────────┐  ┌─────────────────────────────┐     │
│  │  DemographicCard   │  │  RecentOrders              │     │
│  │  col-span-12      │  │  col-span-12               │     │
│  │  xl:col-span-5    │  │  xl:col-span-7             │     │
│  └────────────────────┘  └─────────────────────────────┘     │
└───────────────────────────────────────────────────────────────┘
```

---

## Responsive Breakpoint Behavior

```
┌─────────────────────────────────────────────────────────┐
│  Mobile (< 640px)                                       │
│  ┌───────────────────────────────────────────────────┐ │
│  │  Sidebar: Hidden (slide-in on toggle)            │ │
│  │  Grid: 1 column                                  │ │
│  │  Spacing: p-4, gap-4                             │ │
│  │  Header: Stacked layout                          │ │
│  └───────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│  Tablet (640px - 1024px)                                │
│  ┌───────────────────────────────────────────────────┐ │
│  │  Sidebar: Collapsed (90px) with hover            │ │
│  │  Grid: 2 columns (responsive)                    │ │
│  │  Spacing: p-4 md:p-6, gap-4 md:gap-6            │ │
│  │  Header: Inline layout                           │ │
│  └───────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│  Desktop (>= 1024px)                                    │
│  ┌───────────────────────────────────────────────────┐ │
│  │  Sidebar: Expanded/Collapsed toggle              │ │
│  │  Grid: 3+ columns                                │ │
│  │  Spacing: md:p-6, md:gap-6                       │ │
│  │  Header: Full layout with search                 │ │
│  └───────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

## File Structure Tree

```
src/
├── app/
│   ├── (admin)/                    # Admin route group
│   │   ├── (ui-elements)/         # UI showcase routes
│   │   │   ├── alerts/
│   │   │   ├── avatars/
│   │   │   ├── badge/
│   │   │   ├── buttons/
│   │   │   ├── images/
│   │   │   ├── modals/
│   │   │   └── videos/
│   │   ├── (others-pages)/        # Feature routes
│   │   │   ├── (chart)/
│   │   │   │   ├── bar-chart/
│   │   │   │   └── line-chart/
│   │   │   ├── (forms)/
│   │   │   │   └── form-elements/
│   │   │   ├── (tables)/
│   │   │   │   └── basic-tables/
│   │   │   ├── blank/
│   │   │   ├── calendar/
│   │   │   └── profile/
│   │   ├── layout.tsx             # Admin layout wrapper
│   │   └── page.tsx               # Dashboard page
│   ├── (full-width-pages)/        # Full-width route group
│   │   ├── (auth)/
│   │   │   ├── signin/
│   │   │   ├── signup/
│   │   │   └── layout.tsx
│   │   ├── (error-pages)/
│   │   │   └── error-404/
│   │   └── layout.tsx
│   ├── layout.tsx                 # Root layout
│   ├── globals.css                # Global styles
│   ├── not-found.tsx             # 404 handler
│   └── favicon.ico
│
├── components/
│   ├── auth/                      # Auth components
│   ├── calendar/                  # Calendar components
│   ├── charts/                    # Chart components
│   │   ├── bar/
│   │   └── line/
│   ├── common/                    # Shared components
│   ├── ecommerce/                 # Dashboard components
│   ├── form/                      # Form components
│   │   ├── form-elements/
│   │   ├── group-input/
│   │   ├── input/
│   │   └── switch/
│   ├── header/                    # Header components
│   ├── tables/                    # Table components
│   ├── ui/                        # UI library
│   │   ├── alert/
│   │   ├── avatar/
│   │   ├── badge/
│   │   ├── button/
│   │   ├── dropdown/
│   │   ├── images/
│   │   ├── modal/
│   │   ├── table/
│   │   └── video/
│   ├── user-profile/              # Profile components
│   └── videos/                    # Video components
│
├── context/
│   ├── SidebarContext.tsx         # Sidebar state
│   └── ThemeContext.tsx           # Theme state
│
├── hooks/                         # Custom hooks
│
├── icons/                         # SVG icon components
│
├── layout/                        # Layout components
│   ├── AppHeader.tsx
│   ├── AppSidebar.tsx
│   ├── Backdrop.tsx
│   └── SidebarWidget.tsx
│
└── svg.d.ts                       # SVG type declarations
```

---

## Deployment Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Developer                            │
│  ┌────────────┐                                         │
│  │ Write Code │                                         │
│  └────────────┘                                         │
│        │                                                │
│        ▼                                                │
│  ┌────────────┐                                         │
│  │ npm build  │ → Next.js optimizes and builds         │
│  └────────────┘                                         │
└─────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────┐
│              Build Output (.next/)                      │
│  ├─ Static pages (HTML)                                │
│  ├─ Server components (Node.js)                        │
│  ├─ Client components (JavaScript bundles)             │
│  ├─ Optimized images                                   │
│  ├─ CSS bundles                                        │
│  └─ Static assets                                      │
└─────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────┐
│         Deployment Platform (Vercel, etc.)              │
│  ├─ Edge Network (CDN)                                 │
│  ├─ Serverless Functions (API routes)                 │
│  └─ Server-Side Rendering (SSR)                        │
└─────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────┐
│                    End Users                            │
│  ├─ Fast page loads (static + SSR)                    │
│  ├─ Dynamic content (client-side)                     │
│  └─ Optimized assets (images, fonts)                  │
└─────────────────────────────────────────────────────────┘
```

---

**Documentation Prepared By:** Development Team  
**Last Updated:** November 4, 2025
