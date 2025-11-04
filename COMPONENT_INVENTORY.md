# Component Inventory & Quick Reference

**Project:** TailAdmin Next.js Dashboard  
**Version:** 2.0.2  
**Last Updated:** November 4, 2025

---

## Table of Contents
1. [Layout Components](#layout-components)
2. [UI Components](#ui-components)
3. [Form Components](#form-components)
4. [Chart Components](#chart-components)
5. [Dashboard Components](#dashboard-components)
6. [Page Components](#page-components)
7. [Context Providers](#context-providers)
8. [Custom Hooks](#custom-hooks)

---

## Layout Components

### AppSidebar
**Location:** `src/layout/AppSidebar.tsx`  
**Purpose:** Main navigation sidebar with collapsible functionality  
**Props:** None (uses SidebarContext)

**Features:**
- Collapsible/Expandable (290px ↔ 90px)
- Hover expansion on desktop
- Mobile slide-in drawer
- Nested submenu support
- Active state highlighting
- Custom menu utilities

**Usage:**
```tsx
import AppSidebar from '@/layout/AppSidebar';
<AppSidebar />
```

---

### AppHeader
**Location:** `src/layout/AppHeader.tsx`  
**Purpose:** Top navigation header with search and user actions  
**Props:** None

**Features:**
- Search bar with ⌘K shortcut
- Theme toggle button
- Notification dropdown
- User profile dropdown
- Responsive mobile menu toggle

**Usage:**
```tsx
import AppHeader from '@/layout/AppHeader';
<AppHeader />
```

---

### Backdrop
**Location:** `src/layout/Backdrop.tsx`  
**Purpose:** Mobile overlay for sidebar  
**Props:** None (uses SidebarContext)

**Usage:**
```tsx
import Backdrop from '@/layout/Backdrop';
<Backdrop />
```

---

### SidebarWidget
**Location:** `src/layout/SidebarWidget.tsx`  
**Purpose:** Promotional widget in sidebar footer  
**Props:** None

---

## UI Components

### Button
**Location:** `src/components/ui/button/Button.tsx`

**Props:**
```typescript
interface ButtonProps {
  children: ReactNode;
  size?: "sm" | "md";
  variant?: "primary" | "outline";
  startIcon?: ReactNode;
  endIcon?: ReactNode;
  onClick?: () => void;
  disabled?: boolean;
  className?: string;
}
```

**Usage:**
```tsx
import Button from '@/components/ui/button/Button';

<Button variant="primary" size="md">Click me</Button>
<Button variant="outline" startIcon={<Icon />}>With Icon</Button>
```

---

### Alert
**Location:** `src/components/ui/alert/Alert.tsx`

**Props:**
```typescript
interface AlertProps {
  variant: "success" | "error" | "warning" | "info";
  title: string;
  message: string;
  showLink?: boolean;
  linkHref?: string;
  linkText?: string;
}
```

**Usage:**
```tsx
import Alert from '@/components/ui/alert/Alert';

<Alert 
  variant="success" 
  title="Success!" 
  message="Your changes have been saved."
/>
```

---

### Badge
**Location:** `src/components/ui/badge/Badge.tsx`

**Props:**
```typescript
interface BadgeProps {
  children: React.ReactNode;
  color: "success" | "error" | "warning" | "info" | "default";
}
```

**Usage:**
```tsx
import Badge from '@/components/ui/badge/Badge';

<Badge color="success">Active</Badge>
<Badge color="error">Inactive</Badge>
```

---

### Avatar
**Location:** `src/components/ui/avatar/Avatar.tsx`

**Props:**
```typescript
interface AvatarProps {
  src: string;
  alt: string;
  size?: "sm" | "md" | "lg";
  className?: string;
}
```

**Usage:**
```tsx
import Avatar from '@/components/ui/avatar/Avatar';

<Avatar src="/path/to/image.jpg" alt="User" size="md" />
```

---

### AvatarText
**Location:** `src/components/ui/avatar/AvatarText.tsx`

**Purpose:** Avatar with initials when no image available

**Usage:**
```tsx
import AvatarText from '@/components/ui/avatar/AvatarText';

<AvatarText name="John Doe" size="md" />
```

---

### Dropdown
**Location:** `src/components/ui/dropdown/Dropdown.tsx`

**Features:**
- Click outside to close
- Keyboard navigation
- Custom positioning

**Usage:**
```tsx
import Dropdown from '@/components/ui/dropdown/Dropdown';
import DropdownItem from '@/components/ui/dropdown/DropdownItem';

<Dropdown trigger={<Button>Menu</Button>}>
  <DropdownItem>Item 1</DropdownItem>
  <DropdownItem>Item 2</DropdownItem>
</Dropdown>
```

---

### Modal
**Location:** `src/components/ui/modal/index.tsx`

**Features:**
- Portal rendering
- Backdrop click to close
- Escape key to close
- Focus trap

**Usage:**
```tsx
import Modal from '@/components/ui/modal';

<Modal isOpen={isOpen} onClose={() => setIsOpen(false)}>
  <h2>Modal Title</h2>
  <p>Modal content</p>
</Modal>
```

---

## Form Components

### InputField
**Location:** `src/components/form/input/InputField.tsx`

**Props:**
```typescript
interface InputFieldProps {
  label?: string;
  type?: string;
  placeholder?: string;
  error?: string;
  helperText?: string;
  required?: boolean;
  disabled?: boolean;
  // ... more props
}
```

**Usage:**
```tsx
import InputField from '@/components/form/input/InputField';

<InputField 
  label="Email" 
  type="email" 
  placeholder="you@example.com"
  required
/>
```

---

### TextArea
**Location:** `src/components/form/input/TextArea.tsx`

**Usage:**
```tsx
import TextArea from '@/components/form/input/TextArea';

<TextArea 
  label="Description" 
  rows={4}
  placeholder="Enter description..."
/>
```

---

### Checkbox
**Location:** `src/components/form/input/Checkbox.tsx`

**Usage:**
```tsx
import Checkbox from '@/components/form/input/Checkbox';

<Checkbox 
  label="I agree to terms" 
  checked={agreed}
  onChange={(e) => setAgreed(e.target.checked)}
/>
```

---

### Radio
**Location:** `src/components/form/input/Radio.tsx`

**Usage:**
```tsx
import Radio from '@/components/form/input/Radio';

<Radio name="option" value="1" label="Option 1" />
<Radio name="option" value="2" label="Option 2" />
```

---

### Select
**Location:** `src/components/form/Select.tsx`

**Usage:**
```tsx
import Select from '@/components/form/Select';

<Select 
  label="Country"
  options={[
    { value: 'us', label: 'United States' },
    { value: 'uk', label: 'United Kingdom' },
  ]}
/>
```

---

### MultiSelect
**Location:** `src/components/form/MultiSelect.tsx`

**Features:**
- Multiple selection
- Search/filter
- Custom rendering

---

### DatePicker
**Location:** `src/components/form/date-picker.tsx`

**Library:** Flatpickr

**Usage:**
```tsx
import DatePicker from '@/components/form/date-picker';

<DatePicker 
  placeholder="Select date"
  onChange={(date) => console.log(date)}
/>
```

---

### FileInput
**Location:** `src/components/form/input/FileInput.tsx`

**Usage:**
```tsx
import FileInput from '@/components/form/input/FileInput';

<FileInput 
  label="Upload file"
  accept="image/*"
  onChange={(file) => handleFile(file)}
/>
```

---

### DropZone
**Location:** `src/components/form/form-elements/DropZone.tsx`

**Library:** react-dropzone

**Features:**
- Drag & drop
- Multiple files
- File type validation
- Size limit validation

---

### Switch (Toggle)
**Location:** `src/components/form/switch/Switch.tsx`

**Usage:**
```tsx
import Switch from '@/components/form/switch/Switch';

<Switch 
  checked={enabled}
  onChange={setEnabled}
  label="Enable notifications"
/>
```

---

## Chart Components

### LineChartOne
**Location:** `src/components/charts/line/LineChartOne.tsx`

**Library:** ApexCharts (react-apexcharts)

**Features:**
- Gradient fill
- Responsive
- Custom tooltips
- Dark mode support

**Usage:**
```tsx
import LineChartOne from '@/components/charts/line/LineChartOne';

<LineChartOne />
```

---

### BarChartOne
**Location:** `src/components/charts/bar/BarChartOne.tsx`

**Features:**
- Grouped bars
- Custom colors
- Responsive
- Dark mode support

**Usage:**
```tsx
import BarChartOne from '@/components/charts/bar/BarChartOne';

<BarChartOne />
```

---

## Dashboard Components

### EcommerceMetrics
**Location:** `src/components/ecommerce/EcommerceMetrics.tsx`

**Purpose:** Display key metrics cards (Customers, Orders)

**Usage:**
```tsx
import { EcommerceMetrics } from '@/components/ecommerce/EcommerceMetrics';

<EcommerceMetrics />
```

---

### MonthlySalesChart
**Location:** `src/components/ecommerce/MonthlySalesChart.tsx`

**Purpose:** Monthly sales area chart

---

### MonthlyTarget
**Location:** `src/components/ecommerce/MonthlyTarget.tsx`

**Purpose:** Progress toward monthly goal

---

### StatisticsChart
**Location:** `src/components/ecommerce/StatisticsChart.tsx`

**Purpose:** Multi-metric comparison chart

---

### DemographicCard
**Location:** `src/components/ecommerce/DemographicCard.tsx`

**Purpose:** User demographic breakdown with map

**Features:**
- JVectormap integration
- Demographic statistics
- Interactive regions

---

### RecentOrders
**Location:** `src/components/ecommerce/RecentOrders.tsx`

**Purpose:** Table of recent orders

---

### CountryMap
**Location:** `src/components/ecommerce/CountryMap.tsx`

**Library:** @react-jvectormap

**Features:**
- World map
- Custom regions
- Tooltips
- Click events

---

## Page Components

### Calendar
**Location:** `src/app/(admin)/(others-pages)/calendar/page.tsx`

**Library:** FullCalendar

**Features:**
- Month/Week/Day views
- Drag & drop events
- Event colors
- Add/Edit/Delete events

---

### Profile
**Location:** `src/app/(admin)/(others-pages)/profile/page.tsx`

**Sections:**
- User info
- Edit profile form
- Avatar upload
- Settings

---

### Form Elements
**Location:** `src/app/(admin)/(others-pages)/(forms)/form-elements/page.tsx`

**Demonstrates:**
- All input types
- Input states
- Validation
- Form layouts

---

### Basic Tables
**Location:** `src/app/(admin)/(others-pages)/(tables)/basic-tables/page.tsx`

**Features:**
- Sortable columns
- Pagination
- Row actions
- Responsive

---

## Context Providers

### ThemeProvider
**Location:** `src/context/ThemeContext.tsx`

**Purpose:** Global dark/light theme management

**Usage:**
```tsx
import { useTheme } from '@/context/ThemeContext';

const { theme, toggleTheme } = useTheme();

<button onClick={toggleTheme}>
  {theme === 'dark' ? 'Light' : 'Dark'}
</button>
```

---

### SidebarProvider
**Location:** `src/context/SidebarContext.tsx`

**Purpose:** Sidebar state management

**Usage:**
```tsx
import { useSidebar } from '@/context/SidebarContext';

const { 
  isExpanded, 
  isMobileOpen, 
  toggleSidebar, 
  toggleMobileSidebar 
} = useSidebar();
```

---

## Custom Hooks

### useClickOutside
**Purpose:** Detect clicks outside an element

**Usage:**
```tsx
const ref = useRef(null);
useClickOutside(ref, () => {
  // Handle click outside
});
```

---

## Custom Utilities (CSS)

### Menu Utilities

**Available classes:**
```css
menu-item
menu-item-active
menu-item-inactive
menu-item-icon
menu-item-icon-active
menu-item-icon-inactive
menu-item-arrow
menu-item-arrow-active
menu-item-arrow-inactive
menu-dropdown-item
menu-dropdown-item-active
menu-dropdown-item-inactive
menu-dropdown-badge
menu-dropdown-badge-active
menu-dropdown-badge-inactive
```

**Usage:**
```tsx
<button className="menu-item menu-item-active">
  <span className="menu-item-icon menu-item-icon-active">
    <Icon />
  </span>
  Dashboard
</button>
```

---

### Scrollbar Utilities

**Available classes:**
```css
no-scrollbar       /* Hides scrollbar completely */
custom-scrollbar   /* Styled scrollbar */
```

**Usage:**
```tsx
<div className="overflow-auto custom-scrollbar">
  {/* Content */}
</div>
```

---

## Icon Components

**Location:** `src/icons/`

**Available Icons:**
- ArrowUpIcon / ArrowDownIcon
- GroupIcon
- BoxIconLine / BoxCubeIcon
- GridIcon
- CalendarIcon
- UserCircleIcon
- TableIcon
- PageIcon
- PieChartIcon
- PlugInIcon
- ListIcon
- ChevronDownIcon
- HorizontalDots
- CheckCircle / CheckLine
- Envelope
- Bolt
- Alert
- Folder
- Task
- Videos

**Usage:**
```tsx
import { ArrowUpIcon, GroupIcon } from '@/icons';

<ArrowUpIcon className="w-5 h-5 text-success-500" />
<GroupIcon className="w-6 h-6" />
```

---

## Component File Count

| Category | File Count |
|----------|-----------|
| Layout Components | 4 |
| UI Components | 14+ |
| Form Components | 23+ |
| Chart Components | 2+ |
| Dashboard Components | 7+ |
| Context Providers | 2 |
| Page Components | 10+ |
| Icons | 20+ |

**Total Component Files:** 80+

---

## Styling Quick Reference

### Color Classes

**Brand:**
```
bg-brand-50, bg-brand-100, ..., bg-brand-950
text-brand-50, text-brand-100, ..., text-brand-950
border-brand-50, border-brand-100, ..., border-brand-950
```

**Semantic:**
```
bg-success-50, bg-error-50, bg-warning-50, bg-blue-light-50
text-success-500, text-error-500, text-warning-500
border-success-500, border-error-500, border-warning-500
```

**Gray Scale:**
```
bg-gray-25, bg-gray-50, ..., bg-gray-950
text-gray-700 (primary text)
text-gray-500 (secondary text)
border-gray-200 (light borders)
border-gray-800 (dark borders)
```

---

### Shadow Classes

```
shadow-theme-xs
shadow-theme-sm
shadow-theme-md
shadow-theme-lg
shadow-theme-xl
shadow-focus-ring
```

---

### Typography Classes

**Titles:**
```
text-title-2xl  (72px)
text-title-xl   (60px)
text-title-lg   (48px)
text-title-md   (36px)
text-title-sm   (30px)
```

**Body:**
```
text-theme-xl   (20px)
text-theme-sm   (14px)
text-theme-xs   (12px)
```

---

### Spacing Scale

**Padding/Margin:**
```
p-4 (16px mobile)
md:p-6 (24px desktop)

gap-4 (16px mobile)
md:gap-6 (24px desktop)
```

**Common patterns:**
```
px-4 py-3        (buttons)
px-5 py-3.5      (large buttons)
p-5 md:p-6       (cards)
gap-3            (flex items)
```

---

### Border Radius

```
rounded-lg       (8px - buttons, inputs)
rounded-xl       (12px - alerts, small cards)
rounded-2xl      (16px - large cards)
rounded-full     (badges, avatars)
```

---

## Common Patterns

### Card Pattern
```tsx
<div className="rounded-2xl border border-gray-200 bg-white p-5 
                dark:border-gray-800 dark:bg-white/[0.03] md:p-6">
  {/* Content */}
</div>
```

### Button Pattern
```tsx
<button className="inline-flex items-center justify-center gap-2 
                   rounded-lg bg-brand-500 px-5 py-3.5 text-sm 
                   font-medium text-white shadow-theme-xs 
                   hover:bg-brand-600 focus:ring-2 focus:ring-brand-500/10
                   dark:bg-brand-600 dark:hover:bg-brand-700">
  Button Text
</button>
```

### Input Pattern
```tsx
<input 
  type="text"
  className="w-full rounded-lg border border-gray-200 bg-transparent 
             px-4 py-3 text-sm text-gray-800 
             placeholder:text-gray-400 
             focus:border-brand-300 focus:outline-hidden 
             focus:ring-3 focus:ring-brand-500/10
             dark:border-gray-800 dark:text-white/90 
             dark:placeholder:text-white/30"
/>
```

---

## Quick Start Checklist

When adding new components:

- [ ] Create TypeScript interface for props
- [ ] Include dark mode variants
- [ ] Add responsive breakpoints
- [ ] Include hover/focus states
- [ ] Add disabled state (if applicable)
- [ ] Include loading state (if applicable)
- [ ] Use semantic HTML
- [ ] Add ARIA attributes (if needed)
- [ ] Test keyboard navigation
- [ ] Test with screen reader
- [ ] Document props and usage
- [ ] Add to this inventory

---

**Document Maintained By:** Development Team  
**Last Review:** November 4, 2025
