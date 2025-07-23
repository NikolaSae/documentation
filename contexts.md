# Sekcija: contexts

## `theme-context.tsx`

```ts
// contexts/theme-context.tsx
"use client";

import React, { createContext, useContext, useEffect, useState, useCallback } from 'react';
import { CustomTheme, defaultThemes } from '@/types/theme';

interface ThemeContextType {
  currentTheme: CustomTheme;
  themes: CustomTheme[];
  setTheme: (themeId: string) => void;
  addCustomTheme: (theme: CustomTheme) => void;
  removeCustomTheme: (themeId: string) => void;
  updateCustomTheme: (theme: CustomTheme) => void;
  isCustomTheme: (themeId: string) => boolean;
  isLoaded: boolean;
  isTransitioning: boolean;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
}

export function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [currentTheme, setCurrentTheme] = useState<CustomTheme>(defaultThemes[0]);
  const [cu
```

