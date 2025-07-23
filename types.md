# Sekcija: types

## `theme.ts`

```ts
// types/theme.ts - Updated interface
export interface CustomTheme {
  id: string;
  name: string;
  isDark?: boolean; // Added for proper color scheme detection
  colors: {
    background: string;
    foreground: string;
    card: string;
    cardForeground: string;
    popover: string;
    popoverForeground: string;
    primary: string;
    primaryForeground: string;
    secondary: string;
    secondaryForeground: string;
    muted: string;
    mutedForeground: string;
    accent: string;
    accentForeground: string;
    destructive: string;
    destructiveForeground: string;
    border: string;
    input: string;
    ring: string;
    disguistingGreen: string;
    disguistingGreenDark: string;
    shadowColor: string;
    shadowStrength: string;
  };
}

export const defaultThemes: CustomTheme[] = [
  {
    id: 'light',
    name: 'Light',
    isDark: false,
    colors: {
      background: '0 0% 100%',
      foreground: '240 10% 3.9%',
      card: '0 0% 100%',
      cardForeground: '
```

