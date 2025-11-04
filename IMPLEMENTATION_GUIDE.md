# Implementation Guide & Best Practices

**Document Type:** Practical Implementation Guide  
**Date:** November 4, 2025  
**Project Version:** 2.0.2

---

## Quick Start Implementation Guide

This document provides practical, copy-paste ready implementations for common customizations and enhancements to the TailAdmin dashboard.

---

## 1. Adding ARIA Attributes for Accessibility

### 1.1 Sidebar Toggle Enhancement

**Current Implementation:**
```typescript
// src/layout/AppHeader.tsx
<button onClick={handleToggle}>
  {isMobileOpen ? <CloseIcon /> : <MenuIcon />}
</button>
```

**Enhanced Implementation:**
```typescript
<button
  onClick={handleToggle}
  aria-label={isMobileOpen ? "Close navigation menu" : "Open navigation menu"}
  aria-expanded={isMobileOpen}
  aria-controls="main-sidebar"
  className="..."
>
  {isMobileOpen ? (
    <CloseIcon aria-hidden="true" />
  ) : (
    <MenuIcon aria-hidden="true" />
  )}
</button>
```

### 1.2 Navigation Active State

**Enhanced Implementation:**
```typescript
// src/layout/AppSidebar.tsx
<Link
  href={nav.path}
  aria-current={isActive(nav.path) ? "page" : undefined}
  className={`menu-item group ${
    isActive(nav.path) ? "menu-item-active" : "menu-item-inactive"
  }`}
>
  {nav.name}
</Link>
```

### 1.3 Search Input Enhancement

**Enhanced Implementation:**
```typescript
<input
  ref={inputRef}
  type="text"
  placeholder="Search or type command..."
  aria-label="Search dashboard"
  aria-describedby="search-hint"
  aria-keyshortcuts="Control+K"
  className="..."
/>
<span id="search-hint" className="sr-only">
  Press Control+K or Command+K to focus search
</span>
```

---

## 2. Adding Pre-commit Hooks with Husky

### 2.1 Installation

```bash
# Install dependencies
npm install -D husky lint-staged prettier eslint-config-prettier

# Initialize Husky
npx husky install

# Add prepare script
npm pkg set scripts.prepare="husky install"
```

### 2.2 Configuration

**`.husky/pre-commit`:**
```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx lint-staged
```

**`package.json` addition:**
```json
{
  "lint-staged": {
    "*.{ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{css,md,json}": [
      "prettier --write"
    ]
  }
}
```

**`.prettierrc`:**
```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": false,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "arrowParens": "always"
}
```

**`.prettierignore`:**
```
node_modules
.next
.git
*.md
package-lock.json
```

---

## 3. Testing Infrastructure Setup

### 3.1 Vitest + React Testing Library

**Installation:**
```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom \
  @testing-library/user-event @vitejs/plugin-react jsdom
```

**`vitest.config.ts`:**
```typescript
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    setupFiles: ['./vitest.setup.ts'],
    globals: true,
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});
```

**`vitest.setup.ts`:**
```typescript
import '@testing-library/jest-dom';
import { cleanup } from '@testing-library/react';
import { afterEach } from 'vitest';

afterEach(() => {
  cleanup();
});
```

### 3.2 Example Component Tests

**`src/components/ui/button/__tests__/Button.test.tsx`:**
```typescript
import { describe, it, expect, vi } from 'vitest';
import { render, screen, fireEvent } from '@testing-library/react';
import Button from '../Button';

describe('Button Component', () => {
  it('renders children correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button')).toHaveTextContent('Click me');
  });

  it('applies primary variant by default', () => {
    render(<Button>Click me</Button>);
    const button = screen.getByRole('button');
    expect(button).toHaveClass('bg-brand-500');
  });

  it('applies outline variant when specified', () => {
    render(<Button variant="outline">Click me</Button>);
    const button = screen.getByRole('button');
    expect(button).toHaveClass('ring-gray-300');
  });

  it('handles click events', () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click me</Button>);
    const button = screen.getByRole('button');
    expect(button).toBeDisabled();
    expect(button).toHaveClass('cursor-not-allowed');
  });

  it('renders start icon', () => {
    const Icon = () => <span data-testid="start-icon">→</span>;
    render(<Button startIcon={<Icon />}>Click me</Button>);
    expect(screen.getByTestId('start-icon')).toBeInTheDocument();
  });
});
```

**`src/context/__tests__/ThemeContext.test.tsx`:**
```typescript
import { describe, it, expect, beforeEach } from 'vitest';
import { render, screen, fireEvent } from '@testing-library/react';
import { ThemeProvider, useTheme } from '../ThemeContext';

// Test component
function ThemeConsumer() {
  const { theme, toggleTheme } = useTheme();
  return (
    <div>
      <span data-testid="theme">{theme}</span>
      <button onClick={toggleTheme}>Toggle</button>
    </div>
  );
}

describe('ThemeContext', () => {
  beforeEach(() => {
    localStorage.clear();
  });

  it('provides default theme as light', () => {
    render(
      <ThemeProvider>
        <ThemeConsumer />
      </ThemeProvider>
    );
    expect(screen.getByTestId('theme')).toHaveTextContent('light');
  });

  it('toggles theme on button click', () => {
    render(
      <ThemeProvider>
        <ThemeConsumer />
      </ThemeProvider>
    );
    const button = screen.getByRole('button');
    fireEvent.click(button);
    expect(screen.getByTestId('theme')).toHaveTextContent('dark');
  });

  it('persists theme to localStorage', () => {
    render(
      <ThemeProvider>
        <ThemeConsumer />
      </ThemeProvider>
    );
    fireEvent.click(screen.getByRole('button'));
    expect(localStorage.getItem('theme')).toBe('dark');
  });
});
```

**Update `package.json`:**
```json
{
  "scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest --coverage"
  }
}
```

---

## 4. API Integration Pattern

### 4.1 API Client Setup

**`src/lib/api-client.ts`:**
```typescript
type FetchOptions = RequestInit & {
  params?: Record<string, string>;
};

class ApiClient {
  private baseUrl: string;

  constructor(baseUrl: string) {
    this.baseUrl = baseUrl;
  }

  private async request<T>(
    endpoint: string,
    options: FetchOptions = {}
  ): Promise<T> {
    const { params, ...fetchOptions } = options;
    
    let url = `${this.baseUrl}${endpoint}`;
    
    if (params) {
      const queryString = new URLSearchParams(params).toString();
      url += `?${queryString}`;
    }

    const response = await fetch(url, {
      ...fetchOptions,
      headers: {
        'Content-Type': 'application/json',
        ...fetchOptions.headers,
      },
    });

    if (!response.ok) {
      throw new Error(`API Error: ${response.statusText}`);
    }

    return response.json();
  }

  async get<T>(endpoint: string, params?: Record<string, string>): Promise<T> {
    return this.request<T>(endpoint, { method: 'GET', params });
  }

  async post<T>(endpoint: string, data: unknown): Promise<T> {
    return this.request<T>(endpoint, {
      method: 'POST',
      body: JSON.stringify(data),
    });
  }

  async put<T>(endpoint: string, data: unknown): Promise<T> {
    return this.request<T>(endpoint, {
      method: 'PUT',
      body: JSON.stringify(data),
    });
  }

  async delete<T>(endpoint: string): Promise<T> {
    return this.request<T>(endpoint, { method: 'DELETE' });
  }
}

export const apiClient = new ApiClient(
  process.env.NEXT_PUBLIC_API_URL || 'https://api.example.com'
);
```

### 4.2 Using with Server Components

**`src/app/(admin)/page.tsx`:**
```typescript
import { apiClient } from '@/lib/api-client';

interface DashboardMetrics {
  customers: number;
  orders: number;
  revenue: number;
}

async function getDashboardMetrics(): Promise<DashboardMetrics> {
  return apiClient.get<DashboardMetrics>('/dashboard/metrics');
}

export default async function Dashboard() {
  const metrics = await getDashboardMetrics();
  
  return (
    <div>
      <EcommerceMetrics data={metrics} />
      {/* Other components */}
    </div>
  );
}
```

### 4.3 Using with Client Components (SWR)

**Installation:**
```bash
npm install swr
```

**`src/hooks/useDashboardMetrics.ts`:**
```typescript
'use client';
import useSWR from 'swr';
import { apiClient } from '@/lib/api-client';

interface DashboardMetrics {
  customers: number;
  orders: number;
  revenue: number;
}

const fetcher = (url: string) => apiClient.get<DashboardMetrics>(url);

export function useDashboardMetrics() {
  const { data, error, isLoading, mutate } = useSWR(
    '/dashboard/metrics',
    fetcher,
    {
      refreshInterval: 30000, // Refresh every 30 seconds
      revalidateOnFocus: true,
    }
  );

  return {
    metrics: data,
    isLoading,
    isError: error,
    refresh: mutate,
  };
}
```

**Usage:**
```typescript
'use client';
import { useDashboardMetrics } from '@/hooks/useDashboardMetrics';

export default function LiveMetrics() {
  const { metrics, isLoading, isError } = useDashboardMetrics();

  if (isLoading) return <LoadingSpinner />;
  if (isError) return <ErrorMessage />;

  return <MetricsDisplay data={metrics} />;
}
```

---

## 5. Error Boundary Implementation

### 5.1 Global Error Boundary

**`src/components/common/ErrorBoundary.tsx`:**
```typescript
'use client';
import React, { Component, ReactNode } from 'react';

interface Props {
  children: ReactNode;
  fallback?: ReactNode;
}

interface State {
  hasError: boolean;
  error: Error | null;
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
    // Send to error tracking service (e.g., Sentry)
  }

  render() {
    if (this.state.hasError) {
      if (this.props.fallback) {
        return this.props.fallback;
      }

      return (
        <div className="flex min-h-screen items-center justify-center p-4">
          <div className="max-w-md rounded-2xl border border-gray-200 bg-white p-6 dark:border-gray-800 dark:bg-gray-900">
            <div className="mb-4 flex h-12 w-12 items-center justify-center rounded-xl bg-error-50 dark:bg-error-500/10">
              <svg
                className="h-6 w-6 text-error-500"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
              >
                <path
                  strokeLinecap="round"
                  strokeLinejoin="round"
                  strokeWidth={2}
                  d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"
                />
              </svg>
            </div>
            <h2 className="mb-2 text-xl font-semibold text-gray-800 dark:text-white/90">
              Something went wrong
            </h2>
            <p className="mb-4 text-sm text-gray-500 dark:text-gray-400">
              {this.state.error?.message || 'An unexpected error occurred'}
            </p>
            <button
              onClick={() => this.setState({ hasError: false, error: null })}
              className="w-full rounded-lg bg-brand-500 px-4 py-2.5 text-sm font-medium text-white hover:bg-brand-600"
            >
              Try again
            </button>
          </div>
        </div>
      );
    }

    return this.props.children;
  }
}
```

**Usage in Layout:**
```typescript
// src/app/layout.tsx
import { ErrorBoundary } from '@/components/common/ErrorBoundary';

export default function RootLayout({ children }: { children: ReactNode }) {
  return (
    <html lang="en">
      <body>
        <ErrorBoundary>
          <ThemeProvider>
            <SidebarProvider>{children}</SidebarProvider>
          </ThemeProvider>
        </ErrorBoundary>
      </body>
    </html>
  );
}
```

---

## 6. Form Validation with Zod

### 6.1 Setup

```bash
npm install zod react-hook-form @hookform/resolvers
```

### 6.2 Profile Form Example

**`src/schemas/profileSchema.ts`:**
```typescript
import { z } from 'zod';

export const profileSchema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  email: z.string().email('Invalid email address'),
  bio: z.string().max(500, 'Bio must be less than 500 characters').optional(),
  website: z.string().url('Invalid URL').optional().or(z.literal('')),
  phone: z.string().regex(/^\+?[1-9]\d{1,14}$/, 'Invalid phone number').optional(),
});

export type ProfileFormData = z.infer<typeof profileSchema>;
```

**`src/components/forms/ProfileForm.tsx`:**
```typescript
'use client';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { profileSchema, ProfileFormData } from '@/schemas/profileSchema';
import InputField from '@/components/form/input/InputField';
import TextArea from '@/components/form/input/TextArea';
import Button from '@/components/ui/button/Button';

export default function ProfileForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<ProfileFormData>({
    resolver: zodResolver(profileSchema),
  });

  const onSubmit = async (data: ProfileFormData) => {
    try {
      // Submit to API
      await fetch('/api/profile', {
        method: 'PUT',
        body: JSON.stringify(data),
      });
      // Show success message
    } catch (error) {
      // Show error message
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
      <InputField
        label="Name"
        {...register('name')}
        error={errors.name?.message}
        required
      />
      
      <InputField
        label="Email"
        type="email"
        {...register('email')}
        error={errors.email?.message}
        required
      />
      
      <TextArea
        label="Bio"
        {...register('bio')}
        error={errors.bio?.message}
        rows={4}
      />
      
      <InputField
        label="Website"
        type="url"
        {...register('website')}
        error={errors.website?.message}
      />
      
      <InputField
        label="Phone"
        type="tel"
        {...register('phone')}
        error={errors.phone?.message}
      />
      
      <Button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Saving...' : 'Save Profile'}
      </Button>
    </form>
  );
}
```

---

## 7. Environment Variables Setup

### 7.1 Configuration Files

**`.env.local` (not committed):**
```bash
# API Configuration
NEXT_PUBLIC_API_URL=http://localhost:4000/api
NEXT_PUBLIC_APP_URL=http://localhost:3000

# Feature Flags
NEXT_PUBLIC_ENABLE_ANALYTICS=false
NEXT_PUBLIC_ENABLE_PWA=false

# External Services
NEXT_PUBLIC_GOOGLE_ANALYTICS_ID=
NEXT_PUBLIC_SENTRY_DSN=
```

**`.env.production` (example):**
```bash
# Production API
NEXT_PUBLIC_API_URL=https://api.production.com
NEXT_PUBLIC_APP_URL=https://dashboard.production.com

# Feature Flags
NEXT_PUBLIC_ENABLE_ANALYTICS=true
NEXT_PUBLIC_ENABLE_PWA=true

# External Services
NEXT_PUBLIC_GOOGLE_ANALYTICS_ID=UA-XXXXXXXXX-X
NEXT_PUBLIC_SENTRY_DSN=https://xxx@sentry.io/xxx
```

**`.env.example` (committed for documentation):**
```bash
# Copy this file to .env.local and fill in your values

# API Configuration
NEXT_PUBLIC_API_URL=
NEXT_PUBLIC_APP_URL=

# Feature Flags
NEXT_PUBLIC_ENABLE_ANALYTICS=
NEXT_PUBLIC_ENABLE_PWA=

# External Services
NEXT_PUBLIC_GOOGLE_ANALYTICS_ID=
NEXT_PUBLIC_SENTRY_DSN=
```

### 7.2 Type-safe Environment Variables

**`src/env.ts`:**
```typescript
import { z } from 'zod';

const envSchema = z.object({
  NEXT_PUBLIC_API_URL: z.string().url(),
  NEXT_PUBLIC_APP_URL: z.string().url(),
  NEXT_PUBLIC_ENABLE_ANALYTICS: z.string().transform((val) => val === 'true'),
  NEXT_PUBLIC_ENABLE_PWA: z.string().transform((val) => val === 'true'),
  NEXT_PUBLIC_GOOGLE_ANALYTICS_ID: z.string().optional(),
  NEXT_PUBLIC_SENTRY_DSN: z.string().optional(),
});

export const env = envSchema.parse({
  NEXT_PUBLIC_API_URL: process.env.NEXT_PUBLIC_API_URL,
  NEXT_PUBLIC_APP_URL: process.env.NEXT_PUBLIC_APP_URL,
  NEXT_PUBLIC_ENABLE_ANALYTICS: process.env.NEXT_PUBLIC_ENABLE_ANALYTICS,
  NEXT_PUBLIC_ENABLE_PWA: process.env.NEXT_PUBLIC_ENABLE_PWA,
  NEXT_PUBLIC_GOOGLE_ANALYTICS_ID: process.env.NEXT_PUBLIC_GOOGLE_ANALYTICS_ID,
  NEXT_PUBLIC_SENTRY_DSN: process.env.NEXT_PUBLIC_SENTRY_DSN,
});
```

**Usage:**
```typescript
import { env } from '@/env';

// Type-safe environment variables
const apiUrl = env.NEXT_PUBLIC_API_URL; // ✓ TypeScript knows this is a string
```

---

## 8. Security Headers Configuration

**`next.config.ts` enhancement:**
```typescript
import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  // ... existing config
  
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          {
            key: 'X-DNS-Prefetch-Control',
            value: 'on',
          },
          {
            key: 'Strict-Transport-Security',
            value: 'max-age=63072000; includeSubDomains; preload',
          },
          {
            key: 'X-Frame-Options',
            value: 'SAMEORIGIN',
          },
          {
            key: 'X-Content-Type-Options',
            value: 'nosniff',
          },
          {
            key: 'X-XSS-Protection',
            value: '1; mode=block',
          },
          {
            key: 'Referrer-Policy',
            value: 'origin-when-cross-origin',
          },
          {
            key: 'Permissions-Policy',
            value: 'camera=(), microphone=(), geolocation=()',
          },
        ],
      },
    ];
  },
};

export default nextConfig;
```

---

## 9. Performance Monitoring with Vercel Analytics

### 9.1 Installation

```bash
npm install @vercel/analytics
```

### 9.2 Implementation

**`src/app/layout.tsx`:**
```typescript
import { Analytics } from '@vercel/analytics/react';

export default function RootLayout({ children }: { children: ReactNode }) {
  return (
    <html lang="en">
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  );
}
```

---

## 10. Quick Reference Commands

### Development
```bash
npm run dev              # Start development server
npm run build            # Build for production
npm run start            # Start production server
npm run lint             # Run ESLint
npm run lint -- --fix    # Fix linting issues
```

### Testing (after setup)
```bash
npm test                 # Run tests
npm run test:ui          # Run tests with UI
npm run test:coverage    # Run tests with coverage
```

### Maintenance
```bash
npm audit                # Check vulnerabilities
npm audit fix            # Fix vulnerabilities
npm outdated             # Check outdated packages
npm update               # Update packages
```

---

**Document Created By:** Senior Software Engineer  
**Last Updated:** November 4, 2025  
**Status:** Ready for Implementation
