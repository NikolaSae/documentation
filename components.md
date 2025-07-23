# Sekcija: components

## `BackButton.tsx`

```ts
// Path: components/BackButton.tsx
"use client"; // Ova komponenta koristi hook useRouter, pa mora biti Client Component

import { useRouter } from "next/navigation";
import { Button } from "@/components/ui/button"; // Pretpostavljamo da koristite shadcn/ui Button
import { ArrowLeft } from "lucide-react"; // Pretpostavljamo da koristite lucide-react za ikone
import React from "react";

interface BackButtonProps {
  /** Optional text to display next to the arrow icon. */
  text?: string;
  /** Optional variant for the button (e.g., "ghost", "outline"). */
  variant?: "default" | "secondary" | "destructive" | "outline" | "ghost" | "link";
  /** Optional size for the button (e.g., "default", "sm", "lg", "icon"). */
  size?: "default" | "sm" | "lg" | "icon";
  /** Optional className for custom styling. */
  className?: string;
  /** Optional handler for click event, overrides default back behavior if provided. */
  onClick?: () => void;
  /** Whether the button should be disabled. */
  dis
```

## `EmptyState.tsx`

```ts
// /components/EmptyState.tsx
"use client"; // This is likely a Client Component

import React from 'react';
// You might use Button component from your UI library if you want an action
// import { Button } from "@/components/ui/button";

interface EmptyStateProps {
  title: string;
  description?: string;
  // You can add a prop for an action button if needed
  // actionButton?: React.ReactNode; // Example: <Button onClick={...}>Add Item</Button>
}

const EmptyState: React.FC<EmptyStateProps> = ({ title, description /*, actionButton */ }) => {
  return (
    <div className="flex flex-col items-center justify-center p-8 text-center text-muted-foreground">
      <h3 className="text-xl font-semibold">{title}</h3>
      {description && <p className="mt-2 text-sm">{description}</p>}
      {/* {actionButton && <div className="mt-4">{actionButton}</div>} */}
    </div>
  );
}

export default EmptyState;
```

## `form-error.tsx`

```ts
import { ExclamationTriangleIcon } from "@radix-ui/react-icons";

interface FormErrorProps {
  message?: string;
}

export const FormError = ({ message }: FormErrorProps) => {
  if (!message) return null;

  return (
    <div className="bg-destructive/15 p-3 rounded-md flex items-center gap-x-2 text-sm text-destructive">
      <ExclamationTriangleIcon className="h-4 w-4" />
      <p>{message}</p>
    </div>
  );
};

```

## `form-success.tsx`

```ts
import { CheckCircledIcon } from "@radix-ui/react-icons";

interface FormSuccessProps {
  message?: string;
}

export const FormSuccess = ({ message }: FormSuccessProps) => {
  if (!message) return null;

  return (
    <div className="bg-emerald-500/15 p-3 rounded-md flex items-center gap-x-2 text-sm text-emerald-500">
      <CheckCircledIcon className="h-4 w-4" />
      <p>{message}</p>
    </div>
  );
};

```

## `PageHeader.tsx`

```ts
// /components/PageHeader.tsx
"use client"; // This is a Client Component

import React from 'react';

interface PageHeaderProps {
  title: string;
  description?: string; // Optional description
  // You can add more props here for actions, etc.
  // actions?: React.ReactNode;
}

const PageHeader: React.FC<PageHeaderProps> = ({ title, description /*, actions */ }) => {
  return (
    <div className="pb-4 mb-6 border-b border-gray-200"> {/* Basic styling */}
      <div className="flex items-center justify-between">
        <div>
          <h1 className="text-2xl font-bold tracking-tight">{title}</h1>
          {description && <p className="text-muted-foreground">{description}</p>}
        </div>
        {/* {actions && <div>{actions}</div>} */}
      </div>
    </div>
  );
}

export default PageHeader;
```

## `SearchInput.tsx`

```ts
// components/SearchInput.tsx

"use client";

import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';

export const SearchInput = ({
  placeholder,
  value,
  onChange,
}: {
  placeholder: string;
  value: string;
  onChange: (value: string) => void;
}) => {
  return (
    <div className="relative">
      <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
        <MagnifyingGlassIcon className="h-5 w-5 text-gray-400" />
      </div>
      <input
        type="text"
        placeholder={placeholder}
        value={value}
        onChange={(e) => onChange(e.target.value)}
        className="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-md leading-5 bg-white placeholder-gray-500 focus:outline-none focus:ring-1 focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
      />
    </div>
  );
};
```

## `Table.tsx`

```ts
// components/Table.tsx
"use client";

import { useState } from "react";
import { Complaint } from "@prisma/client";
import { ArrowDown, ArrowUp } from "lucide-react";

type Service = {
  id: string;
  kpi?: string | null;
  status?: string | null;
  remarks?: string | null;
  createdAt: string;
  updatedAt: string;
  complaints: Complaint[];
  [key: string]: any;
};

interface TableProps {
  services: Service[];
  serviceType: string;
}

export function Table({ services, serviceType }: TableProps) {
  const [sortField, setSortField] = useState<string>("createdAt");
  const [sortOrder, setSortOrder] = useState<"asc" | "desc">("asc");

  const toggleSort = (field: string) => {
    if (field === sortField) {
      setSortOrder(sortOrder === "asc" ? "desc" : "asc");
    } else {
      setSortField(field);
      setSortOrder("asc");
    }
  };

  const sortedServices = [...services].sort((a, b) => {
    const aVal = a[sortField];
    const bVal = b[sortField];

    if (aVal === undefined |
```

## `TableHelper.tsx`

```ts
// components/TableHelper.tsx

import React from 'react';
import { Info } from 'lucide-react';

export const getTableHeaders = (activeType: string): string[] => {
  switch (activeType) {
    case 'vas':
      return ['Proizvod', 'Mesec pružanja usluge', 'Jedin. cena', 'Broj transakcija', 'Fakturisan iznos', 'Provajder', 'Akcije'];
    case 'bulk':
      return ['Provider Name', 'Agreement Name', 'Service Name', 'Step Name', 'Sender Name', 'Requests', 'Message Parts', 'Provajder', 'Akcije'];
    case 'parking':
      return ['Naziv', 'Opis', 'Provajder', 'Akcije'];
    case 'human':
      return ['Naziv', 'Opis', 'Tip', 'Provajder', 'Akcije'];
    default:
      return [];
  }
};

export const getTableRow = (service: any, activeType: string, getProviderForService: (id: string) => any, setSelectedService: (service: any) => void): React.ReactNode => {
  switch (activeType) {
    case 'vas':
      return (
        <tr key={service.id} className="bg-white border-b hover:bg-gray-50">
       
```

## `theme-customizer.tsx`

```ts
// components/theme-customizer.tsx

"use client";

import { useState } from 'react';
import { useTheme } from '@/contexts/theme-context';
import { CustomTheme } from '@/types/theme';

interface ThemeCustomizerProps {
  onClose: () => void;
}

export function ThemeCustomizer({ onClose }: ThemeCustomizerProps) {
  const { currentTheme, addCustomTheme, setTheme } = useTheme();
  const [themeName, setThemeName] = useState('');
  const [colors, setColors] = useState(currentTheme.colors);

  const handleColorChange = (colorKey: keyof CustomTheme['colors'], value: string) => {
    const hslValue = hexToHsl(value);
    setColors(prev => ({
      ...prev,
      [colorKey]: hslValue
    }));
  };

  const hexToHsl = (hex: string): string => {
    const r = parseInt(hex.slice(1, 3), 16) / 255;
    const g = parseInt(hex.slice(3, 5), 16) / 255;
    const b = parseInt(hex.slice(5, 7), 16) / 255;

    const max = Math.max(r, g, b);
    const min = Math.min(r, g, b);
    let h = 0, s = 0, l = (max + 
```

## `theme-toggle.tsx`

```ts
// components/theme-toggle.tsx
"use client";

import { useTheme } from '@/contexts/theme-context';
import { useState, useEffect, useRef } from 'react';
import { ThemeCustomizer } from './theme-customizer';

export function ThemeToggle() {
  const { currentTheme, themes, setTheme, isLoaded, isTransitioning } = useTheme();
  const [showCustomizer, setShowCustomizer] = useState(false);
  const [isDropdownOpen, setIsDropdownOpen] = useState(false);
  const dropdownRef = useRef<HTMLDivElement>(null);

  // Close dropdown when clicking outside
  useEffect(() => {
    const handleClickOutside = (event: MouseEvent) => {
      if (dropdownRef.current && !dropdownRef.current.contains(event.target as Node)) {
        setIsDropdownOpen(false);
      }
    };

    if (isDropdownOpen) {
      document.addEventListener('mousedown', handleClickOutside);
      return () => document.removeEventListener('mousedown', handleClickOutside);
    }
  }, [isDropdownOpen]);

  // Close dropdown on escape key
  u
```

## `user-info.tsx`

```ts
import { Card, CardContent, CardHeader } from "@/components/ui/card";
import type { ExtendedUser } from "@/next-auth";
import { Badge } from "@/components/ui/badge";

interface UserInfoProps {
  user?: ExtendedUser;
  label: string;
}

export const UserInfo = ({ user, label }: UserInfoProps) => {
  return (
    <Card className="bg-secondary w-[600px] shadow-md">
      <CardHeader>
        <p className="font-bold text-center">{label}</p>
      </CardHeader>
      <CardContent className="space-y-4">
        <div className="flex flex-row items-center justify-between rounded-lg border p-3 shadow-sm">
          <p className="text-sm font-medium">ID</p>
          <p className="truncate text-xs max-w-[180px] font-mono p-1 bg-zinc-200 rounded-md">
            {user?.id}
          </p>
        </div>

        <div className="flex flex-row items-center justify-between rounded-lg border p-3 shadow-sm">
          <p className="text-sm font-medium">Name</p>
          <p className="truncate text-xs 
```

## `AnomalyDetection.tsx`

```ts
///components/analytics/AnomalyDetection.tsx

"use client";

import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { useState, useEffect } from "react";
import { 
  AlertCircle, 
  TrendingDown, 
  TrendingUp, 
  PieChart, 
  BarChart3, 
  LineChart as LineChartIcon,
  Loader2
} from "lucide-react";
import { LineChart, Line, XAxis, YAxis, Tooltip, CartesianGrid, Legend, ResponsiveContainer } from "recharts";

// Types for anomalies
type AnomalyCategory = 'financial' | 'sales' | 'complaints' | 'providers';

interface Anomaly {
  id: string;
  category: AnomalyCategory;
  metric: string;
  description: string;
  severity: 'low' | 'medium' | 'high' | 'critical';
  detectedAt: string;
  value: number;
  expectedValue: number;
  deviation: number;
  historical?: { date: string; value: number }[];
}

// Mock data - 
```

## `ComplaintAnalytics.tsx`

```ts
// components/analytics/ComplaintAnalytics.tsx
"use client";

import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { LineChart, Line, BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer, PieChart, Pie, Cell } from "recharts";
import { Skeleton } from "@/components/ui/skeleton";
import { ComplaintStatus } from "@prisma/client";
import { ComplaintStats } from "@/actions/analytics/get-complaint-stats";


interface ComplaintAnalyticsProps {
    data: ComplaintStats;
    isLoading: boolean;
    error: any;
}


const STATUS_COLORS = {
    NEW: "#4299e1",
    ASSIGNED: "#805ad5",
    IN_PROGRESS: "#f6ad55",
    PENDING: "#f6e05e",
    RESOLVED: "#68d391",
    CLOSED: "#38a169",
    REJECTED: "#e53e3e"
};

const PRIORITY_COLORS = ["#e53e3e", "#dd6b20", "#ecc94b", "#38a169", "#3182ce"];

function safeArray(arr: any): any[] {
    return Array.isArray(arr) ? arr : [];
}


export default function ComplaintAnalytics({ 
```

## `ContractAnalytics.tsx`

```ts
// components/analytics/ContractAnalytics.tsx
"use client";

import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from "recharts";
import { useContractAnalytics } from "@/hooks/use-contract-analytics";
import { Skeleton } from "@/components/ui/skeleton";

export default function ContractAnalytics() {
  const { data, activeContracts, expiringContracts, isLoading, error } = useContractAnalytics();
  
  if (error) {
    return (
      <Card>
        <CardHeader>
          <CardTitle>Contract Analytics</CardTitle>
          <CardDescription>Error loading contract data</CardDescription>
        </CardHeader>
        <CardContent className="text-red-500">
          Failed to load contract analytics. Please try again later.
        </CardContent>
      </Card>
    );
  }
  
  return (
    <Card className="col-span-4">
      <CardHeader>
        <CardTitle>
```

## `DataFilters.tsx`

```ts
///components/analytics/DataFilters.tsx
"use client";

import { Button } from "@/components/ui/button";
import { Card, CardContent } from "@/components/ui/card";
import { Checkbox } from "@/components/ui/checkbox";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Popover, PopoverContent, PopoverTrigger } from "@/components/ui/popover";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Calendar } from "@/components/ui/calendar";
import {
    CalendarIcon,
    FilterIcon,
    XIcon,
    Search,
    ChevronDown,
    ArrowDownWideNarrow,
    ArrowUpWideNarrow
} from "lucide-react";
import { useState, useEffect } from "react";
import { usePathname, useRouter, useSearchParams } from "next/navigation";
import { format } from "date-fns";
import { cn } from "@/lib/utils";

function safeArray(arr) {
    return Array.isArray(arr) ? arr : [];
}

export interface DataFilterOptions {

```

## `FinancialOverview.tsx`

```ts
// /components/analytics/FinancialOverview.tsx

"use client";

import { useMemo } from "react";
import {
    Card,
    CardContent,
    CardDescription,
    CardHeader,
    CardTitle,
} from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { LineChart, BarChart, Line, Bar, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from "recharts";
import { FinancialMetrics } from "@/actions/analytics/get-financial-data";
import { formatCurrency } from "@/lib/formatters";

function safeArray(arr: any): any[] {
    return Array.isArray(arr) ? arr : [];
}

interface FinancialOverviewProps {
    data: FinancialMetrics;
}

export default function FinancialOverview({ data }: FinancialOverviewProps) {
    const monthlyData = safeArray(data?.revenueByMonth);
    const providerRevenueData = safeArray(data?.providerBreakdown);
    const serviceTypeRevenueData = safeArray(data?.serviceTypeBreakdown);

    const totalRevenue =
```

## `KpiDashboard.tsx`

```ts
///components/analytics/KpiDashboard.tsx


"use client";

import { useEffect, useState } from "react";
import {
    ArrowDownIcon,
    ArrowUpIcon,
    DollarSignIcon,
    ShoppingCartIcon, // Use ShoppingCartIcon for transactions/services
    AlertCircleIcon,
    PercentIcon
} from "lucide-react";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { cn } from "@/lib/utils";
import {
    Tooltip,
    TooltipContent,
    TooltipProvider,
    TooltipTrigger,
} from "@/components/ui/tooltip";
import { formatCurrency } from "@/lib/formatters"; // Assuming formatCurrency exists
// Import the actual types returned by your Server Actions
import { FinancialMetrics } from "@/actions/analytics/get-financial-data";
import { SalesMetrics } from "@/actions/analytics/get-sales-metrics";
import { ComplaintStats } from "@/actions/analytics/get-complaint-stats";

// Update the interface to use the types from your actions
interface KpiDashboardProps 
```

## `RevenueBreakdown.tsx`

```ts
// components/analytics/RevenueBreakdown.tsx
"use client";

import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { PieChart, Pie, Cell, ResponsiveContainer, Tooltip, Legend } from "recharts";
import { useRevenueBreakdown } from "@/hooks/use-revenue-breakdown";
import { Skeleton } from "@/components/ui/skeleton";
import { ServiceType } from "@prisma/client";

const COLORS = ['#0088FE', '#00C49F', '#FFBB28', '#FF8042'];

const RADIAN = Math.PI / 180;
const renderCustomizedLabel = ({ cx, cy, midAngle, innerRadius, outerRadius, percent }: any) => {
  const radius = innerRadius + (outerRadius - innerRadius) * 0.5;
  const x = cx + radius * Math.cos(-midAngle * RADIAN);
  const y = cy + radius * Math.sin(-midAngle * RADIAN);

  return (
    <text x={x} y={y} fill="white" textAnchor={x > cx ? 'start' : 'end'} dominantBaseline="central">
      {`${(percent * 100).toFixed(0)}%`}
    </text>
  );
};

type ServiceTypeLabel = {
  [key in ServiceT
```

## `SalesChart.tsx`

```ts
///components/analytics/SalesChart.tsx

"use client";

import { useState, useEffect } from "react";
import { useRouter } from "next/navigation";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import {
  ResponsiveContainer,
  LineChart,
  Line,
  XAxis,
  YAxis,
  Tooltip,
  CartesianGrid,
  Legend,
  BarChart,
  Bar
} from "recharts";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { useSalesData } from "@/hooks/use-sales-data";
import { Loader2 } from "lucide-react";
import { DataFilterOptions } from "./DataFilters";

export type SalesPeriod = "daily" | "weekly" | "monthly" | "quarterly" | "yearly";

interface SalesDataPoint {
  date: string;
  revenue: number;
  transactions: number;
  providerName?: string;
  serviceName?: string;
  serviceType?: string;
  period?: string;
  collected?: number;
  uncollected?: number;
}

interface SalesMetrics {
  totalRevenue: number;
  totalTransactions: number;
  averageTr
```

## `SalesOverview.tsx`

```ts
// components/analytics/SalesOverview.tsx
"use client";

import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer, BarChart, Bar } from "recharts";
import { useState } from "react";
import { useSalesData } from "@/hooks/use-sales-data";
import { Skeleton } from "@/components/ui/skeleton";

export default function SalesOverview() {
  const [period, setPeriod] = useState<"weekly" | "monthly" | "quarterly">("monthly");
  const { data, isLoading, error } = useSalesData(period);
  
  if (error) {
    return (
      <Card className="col-span-4">
        <CardHeader>
          <CardTitle>Sales Overview</CardTitle>
          <CardDescription>Error loading sales data</CardDescription>
        </CardHeader>
        <CardContent className="text-red-500">
          Failed to load sales d
```

## `ServicePerformance.tsx`

```ts
// components/analytics/ServicePerformance.tsx
"use client";

import { useState } from "react";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from "recharts";
import { useServicePerformance } from "@/hooks/use-service-performance";
import { Skeleton } from "@/components/ui/skeleton";

export default function ServicePerformance() {
  const [serviceType, setServiceType] = useState<string>("all");
  const { data, isLoading, error } = useServicePerformance(serviceType);
  
  if (error) {
    return (
      <Card>
        <CardHeader>
          <CardTitle>Service Performance</CardTitle>
          <CardDescription>Error loading performance data</CardDescription>
        </CardHeader>
        <CardContent className="text-red-500">
          Failed t
```

## `back-button.tsx`

```ts
"use client";

import Link from "next/link";
import { Button } from "@/components/ui/button";

interface BackButtonProps {
  href: string;
  label: string;
}

export const BackButton = ({ href, label }: BackButtonProps) => {
  return (
    <Button variant="link" className="w-full" size="sm" asChild>
      <Link href={href}>{label}</Link>
    </Button>
  );
};

```

## `card-wrapper.tsx`

```ts
"use client";

import {
  Card,
  CardContent,
  CardFooter,
  CardHeader,
} from "@/components/ui/card";
import { Header } from "@/components/auth/header";
import { Social } from "@/components/auth/social";
import { BackButton } from "@/components/auth/back-button";

interface CardWrapperProps {
  children: React.ReactNode;
  headerLabel: string;
  backButtonLabel: string;
  backButtonRef: string;
  showSocial?: boolean;
}

export const CardWrapper = ({
  children,
  headerLabel,
  backButtonLabel,
  backButtonRef,
  showSocial,
}: CardWrapperProps) => {
  return (
    <Card className="w-[400px]">
      <CardHeader>
        <Header label={headerLabel} />
      </CardHeader>
      <CardContent>{children}</CardContent>

      {showSocial && (
        <CardFooter>
          <Social />
        </CardFooter>
      )}

      <CardFooter>
        <BackButton label={backButtonLabel} href={backButtonRef} />
      </CardFooter>
    </Card>
  );
};

```

## `client-side-user-button.tsx`

```ts
// Path: /components/auth/client-side-user-button.tsx
"use client";

import { UserButton } from './user-button';
import { useSession } from "next-auth/react";
import { useEffect, useState } from "react";
import { Skeleton } from "@/components/ui/skeleton";

export function ClientSideUserButton() {
  const { status } = useSession();
  const [mounted, setMounted] = useState(false);

  useEffect(() => {
    setMounted(true);
    console.log("[CLIENT_SIDE_USER_BUTTON] Component mounted");
    console.log("[CLIENT_SIDE_USER_BUTTON] Session status:", status);
  }, [status]);

  // Prevent hydration mismatch
  if (!mounted) {
    return <Skeleton className="w-8 h-8 rounded-full" />;
  }

  return <UserButton />;
}
```

## `error-card.tsx`

```ts
import { CardWrapper } from "@/components/auth/card-wrapper";
import { ExclamationTriangleIcon } from "@radix-ui/react-icons";

export const ErrorCard = () => {
  return (
    <CardWrapper
      headerLabel="Ooops! Something went wrong!"
      backButtonLabel="Back to login"
      backButtonRef="/auth/login"
    >
      <div className="w-full flex items-center justify-center">
        <ExclamationTriangleIcon className="text-destructive" />
      </div>
    </CardWrapper>
  );
};

```

## `header.tsx`

```ts
interface HeaderProps {
  label: string;
}

export const Header = ({ label }: HeaderProps) => {
  return (
    <div className="w-full flex flex-col justify-center items-center gap-y-4">
      <h1 className="text-3xl font-bold">fin-app-hub</h1>
      <p className="text-muted-foreground text-sm">{label}</p>
    </div>
  );
};

```

## `login-button.tsx`

```ts
"use client";

import { useRouter } from "next/navigation";

import { Dialog, DialogContent, DialogTrigger } from "@/components/ui/dialog";
import { LoginForm } from "@/components/auth/login-form";

interface LoginButtonProps {
  children: React.ReactNode;
  mode?: "modal" | "redirect";
  asChild?: boolean;
}

export const LoginButton = ({
  children,
  mode = "redirect",
}: LoginButtonProps) => {
  const router = useRouter();

  const onClick = () => {
    router.push("auth/login");
  };

  if (mode === "modal") {
    return (
      <Dialog>
        <DialogTrigger asChild>{children}</DialogTrigger>
        <DialogContent className="p-0 w-auto bg-transparent border-none">
          <LoginForm />
        </DialogContent>
      </Dialog>
    );
  }

  return (
    <span onClick={onClick} className="cursor-pointer">
      {children}
    </span>
  );
};

```

## `login-form.tsx`

```ts
// components/auth/login-form.tsx

"use client";

import * as z from "zod";

import { useState, useTransition } from "react";
import { useForm } from "react-hook-form";
import { useSearchParams } from "next/navigation";
import { zodResolver } from "@hookform/resolvers/zod";
import { loginSchema } from "@/schemas";

import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { CardWrapper } from "@/components/auth/card-wrapper";
import { FormError } from "@/components/form-error";
import { FormSuccess } from "@/components/form-success";
import { login } from "@/actions/login";
import Link from "next/link";

export const LoginForm = () => {
  const searchParams = useSearchParams();
  const callbackUrl = searchParams.get("callbackUrl");
  const urlError =
    searchParams.get("error") === "OAuthAccountNotLinked"
      ? "Email alrea
```

## `logout-button.tsx`

```ts
"use client";

import { logout } from "@/actions/logout";

interface LogoutButtonProps {
  children: React.ReactNode;
}

export const LogoutButton = ({ children }: LogoutButtonProps) => {
  const onClick = () => {
    logout();
  };
  return (
    <span onClick={onClick} className="cursor-pointer">
      {children}
    </span>
  );
};

```

## `new-password-form.tsx`

```ts
"use client";

import * as z from "zod";

import { useState, useTransition } from "react";
import { useForm } from "react-hook-form";
import { useSearchParams } from "next/navigation";
import { zodResolver } from "@hookform/resolvers/zod";
import { NewPasswordSchema } from "@/schemas";

import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { CardWrapper } from "@/components/auth/card-wrapper";
import { FormError } from "@/components/form-error";
import { FormSuccess } from "@/components/form-success";
import { newPassword } from "@/actions/password";

export const NewPasswordForm = () => {
  const searchParams = useSearchParams();

  const token = searchParams.get("token");

  const [error, setError] = useState<string | undefined>("");
  const [success, setSuccess] = useState<string | undefined>("");
  const [isPending, sta
```

## `new-verification-form.tsx`

```ts
"use client";

import { useCallback, useEffect, useState } from "react";
import { useSearchParams } from "next/navigation";
import { CardWrapper } from "./card-wrapper";
import { Loader2 } from "lucide-react";
import { newVerification } from "@/actions/new-verification";
import { FormError } from "@/components/form-error";
import { FormSuccess } from "@/components/form-success";

export const NewVerifictionForm = () => {
  const [error, setError] = useState<string | undefined>("");
  const [success, setSuccess] = useState<string | undefined>("");

  const searchParams = useSearchParams();

  const token = searchParams.get("token");

  const onSubmit = useCallback(() => {
    if (success || error) return;

    if (!token) {
      setError("Missing token!");
      return;
    }

    newVerification(token)
      .then((data) => {
        setSuccess(data.success);
        setError(data.error);
      })
      .catch(() => {
        setError("Something went wrong!");
      });
  }, [token, s
```

## `register-form.tsx`

```ts
"use client";

import * as z from "zod";

import { useState, useTransition } from "react";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { RegisterSchema } from "@/schemas";

import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { CardWrapper } from "@/components/auth/card-wrapper";
import { FormError } from "@/components/form-error";
import { FormSuccess } from "../form-success";
import { register } from "@/actions/register";

export const RegisterForm = () => {
  const [error, setError] = useState<string | undefined>("");
  const [success, setSuccess] = useState<string | undefined>("");
  const [isPending, startTransition] = useTransition();

  const form = useForm<z.infer<typeof RegisterSchema>>({
    resolver: zodResolver(RegisterSchema),
    defaultValues: {
  
```

## `reset-form.tsx`

```ts
"use client";

import * as z from "zod";

import { useState, useTransition } from "react";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { ResetSchema } from "@/schemas";

import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { CardWrapper } from "@/components/auth/card-wrapper";
import { FormError } from "@/components/form-error";
import { FormSuccess } from "@/components/form-success";
import { reset } from "@/actions/reset";

export const ResetForm = () => {
  const [error, setError] = useState<string | undefined>("");
  const [success, setSuccess] = useState<string | undefined>("");
  const [isPending, startTransition] = useTransition();

  const form = useForm<z.infer<typeof ResetSchema>>({
    resolver: zodResolver(ResetSchema),
    defaultValues: {
      emai
```

## `role-gate.tsx`

```ts
//components/auth/role-gate.tsx

"use client";

import { useCurrentRole } from "@/hooks/use-current-role";
import { UserRole } from "@prisma/client";
import { FormError } from "../form-error";

interface RoleGateProps {
  children: React.ReactNode;
  alloweRole: UserRole;
}

export const RoleGate = ({ children, alloweRole }: RoleGateProps) => {
  const role = useCurrentRole();

  if (role !== alloweRole) {
    return (
      <FormError message="You do not have permission to view this content!" />
    );
  }

  return <>{children}</>;
};

```

## `social.tsx`

```ts
"use client";

import { useSearchParams } from "next/navigation";
import { signIn } from "next-auth/react";

import { Button } from "@/components/ui/button";
import { DEFAULT_LOGIN_REDIRECT } from "@/routes";

import { FcGoogle } from "react-icons/fc";
import { FaGithub } from "react-icons/fa";

export const Social = () => {
  const searchParams = useSearchParams();
  const callbackUrl = searchParams.get("callbackUrl");

  const onClick = (provider: "google" | "github") => {
    signIn(provider, {
      callbackUrl: callbackUrl || DEFAULT_LOGIN_REDIRECT,
    });
  };

  return (
    <div className="flex items-center w-full gap-x-2">
      <Button
        size="lg"
        className="w-full"
        variant="outline"
        onClick={() => onClick("google")}
      >
        <FcGoogle className="h-5 w-5" />
      </Button>
      <Button
        size="lg"
        className="w-full"
        variant="outline"
        onClick={() => onClick("github")}
      >
        <FaGithub className="h-5
```

## `user-button.tsx`

```ts
// Path: /components/auth/user-button.tsx
"use client";

import { useSession, signOut } from "next-auth/react";
import { useEffect, useState } from "react";
import { LogOut, User, Settings } from "lucide-react";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar";
import { Button } from "@/components/ui/button";

export function UserButton() {
  const { data: session, status } = useSession();
  const [mounted, setMounted] = useState(false);

  // Ensure component is mounted to prevent hydration issues
  useEffect(() => {
    setMounted(true);
  }, []);

  // Debug session information
  useEffect(() => {
    console.log("[USER_BUTTON] ===== SESSION DEBUG =====");
    console.log("[USER_BUTTON] Status:", status);
    console.log("[USER_BUTTON] Session:", session);
    console.log("[USER_BU
```

## `BlacklistFilters.tsx`

```ts
// components/blacklist/BlacklistFilters.tsx
"use client";

import React, { useState, useEffect } from 'react';
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Button } from "@/components/ui/button";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Calendar } from "@/components/ui/calendar";
import { Popover, PopoverContent, PopoverTrigger } from "@/components/ui/popover";
import { CalendarIcon, X } from "lucide-react";
import { format } from "date-fns";
import { BlacklistFilters } from "@/hooks/use-sender-blacklist";
import { useProviders } from "@/hooks/use-providers";

interface BlacklistFiltersComponentProps {
  initialFilters: BlacklistFilters;
  onFilterChange: (filters: BlacklistFilters) => void;
}

export function BlacklistFiltersComponent({ 
  initialFilters, 
  onFilterChange 
}: BlacklistFiltersComponentProps) {
  // Osiguravamo da localFilters nikad nije un
```

## `BlacklistSection.tsx`

```ts
//components/blacklist/BlacklistSection.tsx
"use client";

import { useState, useMemo } from "react";
import { useRouter } from "next/navigation";
import { Button } from "@/components/ui/button";
import { Plus, Shield, AlertTriangle, RefreshCw, FileText } from "lucide-react";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import { SenderBlacklistTable } from "./SenderBlacklistTable";
import { BlacklistFiltersComponent } from "./BlacklistFilters";
import { useSenderBlacklist } from "@/hooks/use-sender-blacklist";
import CreateBlacklistEntryDialog from "./CreateBlacklistEntryDialog"; // Fixed import
import { useCheckBlacklistedSenders } from "@/hooks/use-check-blacklisted-senders";

export function BlacklistSection() {
  const router = useRouter();
  const [showCreateDialog, setShowCreateDialog] = useState(false);
  const { 
    entries, 
    totalCount, 
    isLoading, 
    filters, 
    pa
```

## `CreateBlacklistEntryDialog.tsx`

```ts
// components/blacklist/CreateBlacklistEntryDialog.tsx
// components/blacklist/CreateBlacklistEntryDialog.tsx
"use client";

import { useState } from "react";
import { Dialog, DialogContent, DialogDescription, DialogFooter, DialogHeader, DialogTitle } from "@/components/ui/dialog";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Textarea } from "@/components/ui/textarea";
import { Calendar } from "@/components/ui/calendar";
import { Popover, PopoverContent, PopoverTrigger } from "@/components/ui/popover";
import { CalendarIcon, Loader2 } from "lucide-react";
import { format } from "date-fns";
import { createBlacklistEntry } from "@/actions/blacklist/create-blacklist-entry";
import { toast } from "sonner";
import { Switch } from "@/components/ui/switch";

interface CreateBlacklistEntryDialogProps {
  open: boolean;
  onOpenChange: (open: boolean) => void;
  onSuccess?: () => void;
}


```

## `SenderBlacklistTable.tsx`

```ts
// components/blacklist/SenderBlacklistTable.tsx

"use client";

import React from 'react';
import { format } from "date-fns";
import { Badge } from "@/components/ui/badge";
import { Button } from "@/components/ui/button";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { Pagination, PaginationContent, PaginationItem, PaginationLink, PaginationNext, PaginationPrevious } from "@/components/ui/pagination";
import { Loader2, Eye, EyeOff, Trash2, ArrowUpDown, ArrowUp, ArrowDown } from "lucide-react";
import { toast } from "sonner";
import { SenderBlacklistEntry } from "@/lib/types/blacklist";
import { updateBlacklistEntry } from "@/actions/blacklist/update-blacklist-entry";
import { deleteBlacklistEntry } from "@/actions/blacklist/delete-blacklist-entry";
import { BlacklistPagination } from "@/hooks/use-sender-blacklist";
import EmptyState from "@/components/EmptyState";

type SortField = 'senderName' | 'effectiveDa
```

## `BulkServiceCard.tsx`

```ts
//components/bulk-services/BulkServiceCard.ts

"use client";

import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from "@/components/ui/card";
import { BulkService } from "@prisma/client";
import { CalendarIcon, BarChart2, MessageSquare, Users } from "lucide-react";
import { formatDate } from "@/lib/utils";
import { Badge } from "@/components/ui/badge";
import Link from "next/link";
import { Button } from "@/components/ui/button";

interface BulkServiceCardProps {
  bulkService: BulkService & {
    provider: { name: string };
    service: { name: string };
  };
}

export function BulkServiceCard({ bulkService }: BulkServiceCardProps) {
  return (
    <Card className="h-full overflow-hidden hover:border-primary/50 transition-colors">
      <CardHeader className="pb-3">
        <div className="flex justify-between items-start">
          <CardTitle className="text-lg font-semibold line-clamp-1">
            {bulkService.service_name}
          </CardTitle>
  
```

## `BulkServiceDetails.tsx`

```ts
//components/bulk-services/BulkServiceDetails.tsx


"use client";

import { useState } from "react";
import { useRouter } from "next/navigation";
import { 
  CalendarIcon, 
  ArrowRightIcon, 
  MessageCircleIcon, 
  CheckCircleIcon,
  EditIcon,
  TrashIcon,
  AlertCircleIcon
} from "lucide-react";
import { toast } from "sonner";

import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from "@/components/ui/card";
import { Separator } from "@/components/ui/separator";
import { Button } from "@/components/ui/button";
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
} from "@/components/ui/dialog";
import { Badge } from "@/components/ui/badge";

import { formatDate } from "@/lib/utils";
import { deleteBulkService } from "@/actions/bulk-services";
import { BulkService, Provider, Service } from "@prisma/client";

interface ExtendedBulkService extends BulkService {
  provider: Provider;
  service: Service;
}

int
```

## `BulkServiceFilters.tsx`

```ts
//components/bulk-services/BulkServiceFilters.tsx


"use client";

import { useState, useEffect } from "react";
import { usePathname, useRouter, useSearchParams } from "next/navigation";
import { SearchIcon, FilterIcon, X, ChevronDown } from "lucide-react";

import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import {
  Popover,
  PopoverContent,
  PopoverTrigger,
} from "@/components/ui/popover";
import { Badge } from "@/components/ui/badge";
import { Separator } from "@/components/ui/separator";
import { Provider, Service } from "@prisma/client";

interface BulkServiceFiltersProps {
  providers?: Provider[];
  services?: Service[];
}

export const BulkServiceFilters = ({
  providers = [],
  services = []
}: BulkServiceFiltersProps) => {
  const router = useRouter();
  const pathname = usePathname();
  const searchParams = useSe
```

## `BulkServiceForm.tsx`

```ts
//components/bulk-services/BulkServiceForm.tsx

"use client";

import { useState } from "react";
import { zodResolver } from "@hookform/resolvers/zod";
import { useForm } from "react-hook-form";
import { useRouter } from "next/navigation";
import { toast } from "sonner";

import { Button } from "@/components/ui/button";
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Spinner } from "@/components/ui/spinner";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";

import { BulkServiceFormValues, bulkServiceSchema } from "@/schemas/bulk-service";
import { createBulkService, updateBulkService } from "@/actions/bulk-services";
import { Service, Provider } from "@prisma/client";

interface BulkServiceFormProps {
  initialD
```

## `BulkServiceList.tsx`

```ts
//components/bulk-services/BulkServiceList.tsx


"use client";

import { useState } from "react";
import { useRouter, useSearchParams } from "next/navigation";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { Button } from "@/components/ui/button";
import { Badge } from "@/components/ui/badge";
import { Card, CardContent, CardFooter } from "@/components/ui/card";
import { Loader2, Eye, Edit, AlertTriangle } from "lucide-react";
import { format } from "date-fns";
import Link from "next/link";
import { useBulkServices } from "@/hooks/use-bulk-services";
import { Input } from "@/components/ui/input";
import {
  Pagination,
  PaginationContent,
  PaginationItem,
  PaginationNext,
  PaginationPrevious,
} from "@/components/ui/pagination";
import { BulkServiceWithRelations } from "@/lib/types/bulk-service-types";

export default function BulkServiceList() {
  const router = useRouter();
  const searchParams = useSear
```

## `BulkServiceStats.tsx`

```ts
///components/bulk-services/BulkServiceStats.tsx

"use client";

import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer, PieChart, Pie, Cell, Legend } from "recharts";
import { useTheme } from "next-themes";
import { useEffect, useState } from "react";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";

interface BulkServiceStatsProps {
  bulkServices: Array<{
    id: string;
    provider_name: string;
    agreement_name: string;
    service_name: string;
    step_name: string;
    sender_name: string;
    requests: number;
    message_parts: number;
    createdAt: Date;
  }>;
}

export function BulkServiceStats({ bulkServices }: BulkServiceStatsProps) {
  const { theme } = useTheme();
  const [mounted, setMounted] = useState(false);
  const [timeR
```

## `ImportForm.tsx`

```ts
///components/bulk-services/ImportForm.tsx

"use client";

import { useState, useRef } from "react";
import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { AlertCircle, CheckCircle, FileText, Upload, X } from "lucide-react";
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert";
import { Progress } from "@/components/ui/progress";
import { importBulkServicesFromCsv } from "@/actions/bulk-services/import";
import { toast } from "sonner";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { BulkServiceImportResult } from "@/lib/types/bulk-service-types";
import { Badge } from "@/components/ui/badge";
import { TableCell, TableRow } from "@/components/ui/table"; // Dodajte ove import-e ako nedostaju

interface ImportFormProps {
  onSuccess?: (count: number) => void;
  onCancel?: () => void;
}

export function ImportForm(
```

## `AmountSummary.tsx`

```ts
  // components/complaints/AmountSummary.tsx

  import { useMemo } from 'react';
  import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
  import { DollarSign, TrendingUp, TrendingDown, BarChart2 } from 'lucide-react';
  import { Complaint } from '@prisma/client';
  import { formatCurrency } from '@/utils/format';

  interface AmountSummaryProps {
    complaints: Complaint[];
    timeframe?: 'weekly' | 'monthly' | 'quarterly' | 'yearly';
  }

  export function AmountSummary({ complaints, timeframe = 'monthly' }: AmountSummaryProps) {
    const summary = useMemo(() => {
      if (!complaints || complaints.length === 0) {
        return {
          totalImpact: 0,
          resolvedImpact: 0,
          pendingImpact: 0,
          averageImpact: 0,
        };
      }

      const totalImpact = complaints.reduce((sum, complaint) => 
        sum + (complaint.financialImpact || 0), 0);
      
      const resolvedComplaints = complaints.filter(
        c => c.status
```

## `AssignComplaint.tsx`

```ts
// Path: components/complaints/AssignComplaint.tsx
"use client";

import { useState, useTransition, useEffect } from "react";
import { Complaint } from "@prisma/client";
import { Button } from "@/components/ui/button";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { toast } from "sonner";
import { assignComplaint } from "@/actions/complaints/assign"; // Import the assignment action
import { User } from "next-auth"; // Assuming User type from next-auth

// Define a type for the assignable users data
interface AssignableUser {
    id: string;
    name: string | null;
}

interface AssignComplaintProps {
    complaintId: string;
    currentAssignedAgentId: string | null;
    assignableUsers: AssignableUser[]; // List of users who can be assigned
    canAssign: boolean; // Permission flag from parent
    onAssignmentComplete: () => void; // Callba
```

## `CommentSection.tsx`

```ts
// /components/complaints/CommentSection.tsx
"use client";

import { useState } from "react";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";
import { formatDistanceToNow } from "date-fns";
import { Comment, User } from "@prisma/client";
import { Button } from "@/components/ui/button";
import { Textarea } from "@/components/ui/textarea";
import { Checkbox } from "@/components/ui/checkbox";
import { 
  Form, 
  FormControl, 
  FormField, 
  FormItem, 
  FormMessage 
} from "@/components/ui/form";
import { 
  Card, 
  CardContent, 
  CardFooter, 
  CardHeader 
} from "@/components/ui/card";
import { Avatar, AvatarFallback } from "@/components/ui/avatar";
import { Badge } from "@/components/ui/badge";
import { addComment } from "@/actions/complaints/comment";
import { useToast } from "@/components/toast/toast-context";

type CommentWithUser = Comment & { user: Pick<User, "id" | "name" | "email"> };

interface Comme
```

## `ComplaintFilters.tsx`

```ts
// /components/complaints/ComplaintFilters.tsx
"use client";

import { useState, useEffect, useCallback } from "react";
import { ComplaintStatus } from "@prisma/client";
import { Button } from "@/components/ui/button";
import { Label } from "@/components/ui/label";
import {
  Card,
  CardContent,
  CardFooter,
  CardHeader,
  CardTitle
} from "@/components/ui/card";
import { getServicesByCategory } from "@/actions/services/get-by-category";
import { getProvidersByService } from "@/actions/providers/get-by-service";
import ServiceCategoryFilter from "./ServiceCategoryFilter";
import ProviderFilter from "./ProviderFilter";
import DateRangeFilter from "./DateRangeFilter";
import { X, Filter, Loader2 } from "lucide-react"; // Dodat Loader2
// IMPORT useSession hook
import { useSession } from "next-auth/react"; // Prilagodite putanju ako je potrebno

export interface ComplaintFiltersState {
  statuses: ComplaintStatus[];
  serviceId?: string;
  providerId?: string;
  dateRange?: {
    from?
```

## `ComplaintForm.tsx`

```ts
//components/complaints/ComplaintForm.tsx
"use client";

import { useState, useEffect } from 'react';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { Complaint } from '@prisma/client';
import { ComplaintSchema, ComplaintFormData } from '@/schemas/complaint';
import { ServiceSelection } from './ServiceSelection';
import { FileUpload } from './FileUpload';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Textarea } from '@/components/ui/textarea';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';
import { Form, FormControl, FormField, FormItem, FormLabel, FormMessage } from '@/components/ui/form';
import { Card, CardContent, CardFooter, CardHeader, CardTitle } from '@/components/ui/card';
import { RadioGroup, RadioGroupItem } from '@/components/ui/radio-group';

const ServiceTypeEnum = {
  PROVIDER: 'PROVIDER',
  HUMANITA
```

## `ComplaintFormWrapper.tsx`

```ts
// Path: components/complaints/ComplaintFormWrapper.tsx
// components/complaints/ComplaintFormWrapper.tsx
"use client";

import { useTransition } from "react";
import { useRouter } from "next/navigation";
import { ComplaintForm } from "@/components/complaints/ComplaintForm";
import { createComplaint } from "@/actions/complaints/create";
import { ComplaintFormData } from "@/schemas/complaint";
import { toast } from "sonner";
import { Button } from "@/components/ui/button";
import { ArrowLeft } from "lucide-react";
import { type getProviders } from "@/actions/complaints/providers";
import { signOut } from "next-auth/react";

// Add these types to match the updated props
interface ComplaintFormWrapperProps {
    providersData: Awaited<ReturnType<typeof getProviders>>;
    humanitarianOrgsData: { id: string; name: string }[];
    parkingServicesData: { id: string; name: string }[];
    onCancel?: () => void;
}

export function ComplaintFormWrapper({ 
    providersData,
    humanitarianOrgs
```

## `ComplaintList.tsx`

```ts
// Fixed ComplaintList Component
"use client";

import { useState } from "react";
import { useRouter } from "next/navigation";
import { formatDistanceToNow } from "date-fns";
import {
  Complaint,
  Service,
  Product,
  Provider,
  User,
  ComplaintStatus
} from "@prisma/client";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow
} from "@/components/ui/table";
import { Button } from "@/components/ui/button";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger
} from "@/components/ui/dropdown-menu";
import {
  Pagination,
  PaginationContent,
  PaginationItem,
  PaginationLink,
  PaginationNext,
  PaginationPrevious,
} from "@/components/ui/pagination";
import StatusBadge from "./StatusBadge";
import { Eye, MoreVertical, Pencil, Trash2 } from "lucide-react";
import { deleteComplaint } from "@/actions/complaints/delete";
import { toast } from "sonner";

import {
  AlertDialog,
  AlertDialogAction,
  AlertDialogCancel
```

## `ComplaintTimeline.tsx`

```ts
// components/complaints/ComplaintTimeline.tsx

import { useMemo } from 'react';
import { format } from 'date-fns';
import { ComplaintStatusHistory, User } from '@prisma/client';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
// ISPRAVLJENO: Promenjen uvoz imena funkcije
import { getStatusColorClass, getStatusLabel } from '@/utils/complaint-status';
import { CheckCircle, AlertCircle, Clock, ArrowRight } from 'lucide-react';

interface ComplaintTimelineProps {
  statusHistory: (ComplaintStatusHistory & {
    changedBy?: Pick<User, 'id' | 'name' | 'email'>;
  })[];
}

export function ComplaintTimeline({ statusHistory }: ComplaintTimelineProps) {
  const sortedHistory = useMemo(() => {
    return [...statusHistory].sort((a, b) =>
      new Date(a.changedAt).getTime() - new Date(b.changedAt).getTime()
    );
  }, [statusHistory]);

  const getStatusIcon = (status: string) => {
    switch (status) {
      case 'RESOLVED':
      case 'CLOSED':
        return
```

## `CsvImport.tsx`

```ts
// components/complaints/CsvImport.tsx

"use client";

import { useState, useRef } from "react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { X, Upload, FileSpreadsheet, CheckCircle, AlertCircle } from "lucide-react";
import { importComplaints } from "@/actions/complaints/import";

interface CsvImportProps {
  onClose: () => void;
  onComplete: (success: boolean, message: string) => void;
}

export function CsvImport({ onClose, onComplete }: CsvImportProps) {
  const [file, setFile] = useState<File | null>(null);
  const [isUploading, setIsUploading] = useState(false);
  const [validationStatus, setValidationStatus] = useState<{
    isValid: boolean;
    message: string;
  } | null>(null);
  const fileInputRef = useRef<HTMLInputElement>(null);

  const handleFileChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const selectedFile = e.target.files?.[0];
    
    if (!selectedFile) return;
    
    // Validate file type
```

## `DateRangeFilter.tsx`

```ts
// /components/complaints/DateRangeFilter.tsx
"use client";
import { format } from "date-fns";
import { Calendar as CalendarIcon } from "lucide-react";
import { DateRange } from "react-day-picker";
import { cn } from "@/lib/utils";
import { Button } from "@/components/ui/button";
import { Calendar } from "@/components/ui/calendar";
import {
  Popover,
  PopoverContent,
  PopoverTrigger,
} from "@/components/ui/popover";

interface DateRangeFilterProps {
  startDate?: Date;
  endDate?: Date;
  onChange: (from?: Date, to?: Date) => void;
}

// Use named export instead of default export
export function DateRangeFilter({
  startDate,
  endDate,
  onChange,
}: DateRangeFilterProps) {
  const dateRange: DateRange | undefined = startDate || endDate 
    ? { 
        from: startDate || undefined, 
        to: endDate || undefined 
      }
    : undefined;
  return (
    <div className="grid gap-2">
      <Popover>
        <PopoverTrigger asChild>
          <Button
            id="date"
       
```

## `FileUpload.tsx`

```ts
// /components/complaints/FileUpload.tsx
"use client";

import { useState, useRef } from "react";
import { useRouter } from "next/navigation";
import { Button } from "@/components/ui/button";
import { Progress } from "@/components/ui/progress";
import { Attachment } from "@prisma/client";
import { Upload, X, FileIcon, FileText } from "lucide-react";
import { toast } from "sonner";

interface FileUploadProps {
  complaintId?: string;
  existingAttachments?: Attachment[];
}

// KLJUČNA IZMENA: Menjamo default export u named export
export function FileUpload({
  complaintId,
  existingAttachments = []
}: FileUploadProps) {
  const router = useRouter();
  // const { toast } = useToast();

  const [files, setFiles] = useState<File[]>([]);
  const [uploading, setUploading] = useState(false);
  const [uploadProgress, setUploadProgress] = useState(0);
  const fileInputRef = useRef<HTMLInputElement>(null);

  const handleFileChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    if (e.targe
```

## `NotificationBanner.tsx`

```ts
// components/complaints/NotificationBanner.tsx


import { useState, useEffect } from 'react';
import { Alert, AlertDescription, AlertTitle } from '@/components/ui/alert';
import { X, CheckCircle, AlertCircle, Info, Bell } from 'lucide-react';
import { Button } from '@/components/ui/button';
import { getStatusLabel } from '@/utils/complaint-status';
import { ComplaintStatus } from '@prisma/client';

interface NotificationProps {
  title: string;
  message: string;
  type: 'success' | 'error' | 'warning' | 'info';
  autoClose?: boolean;
  duration?: number;
  onClose?: () => void;
}

export function NotificationBanner({ 
  title, 
  message, 
  type = 'info',
  autoClose = true,
  duration = 5000,
  onClose
}: NotificationProps) {
  const [visible, setVisible] = useState(true);

  useEffect(() => {
    let timer: NodeJS.Timeout;
    if (autoClose) {
      timer = setTimeout(() => {
        setVisible(false);
        if (onClose) onClose();
      }, duration);
    }
    return () => {
  
```

## `ProductSelection.tsx`

```ts
// components/complaints/ProductSelection.tsx


"use client";

import { useState, useEffect } from 'react';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';
import { FormControl } from '@/components/ui/form';
import { getProductsByServiceId } from "@/actions/complaints/products";
import { Product } from '@prisma/client';

interface ProductSelectionProps {
  serviceId: string | null | undefined;
  selectedProductId: string;
  onProductSelect: (productId: string) => void;
}

export function ProductSelection({ serviceId, selectedProductId, onProductSelect }: ProductSelectionProps) {
  const [products, setProducts] = useState<Product[]>([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    if (!serviceId) {
      setProducts([]);
      // onProductSelect(''); // Optional: uncomment to reset form value
      return;
    }

    const fetchProducts = a
```

## `ProviderFilter.tsx`

```ts
// /components/complaints/ProviderFilter.tsx
"use client";

import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";

interface Provider {
  id: string;
  name: string;
}

interface ProviderFilterProps {
  providers: Provider[];
  selectedProviderId?: string;
  onChange: (providerId: string | null) => void;
  disabled?: boolean;
}

export default function ProviderFilter({
  providers,
  selectedProviderId,
  onChange,
  disabled = false,
}: ProviderFilterProps) {
  return (
    <Select
      value={selectedProviderId || ""}
      onValueChange={(value) => onChange(value || null)}
      disabled={disabled || providers.length === 0}
    >
      <SelectTrigger>
        <SelectValue placeholder={disabled ? "Select a service first" : "All Providers"} />
      </SelectTrigger>
      <SelectContent>
        <SelectItem value="">All Providers</SelectItem>
        {providers.map((provider) => (
          <SelectItem key={provider.id} val
```

## `ServiceCategoryFilter.tsx`

```ts
// /components/complaints/ServiceCategoryFilter.tsx
"use client";

import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";

interface Service {
  id: string;
  name: string;
}

interface ServiceCategoryFilterProps {
  services: Service[];
  selectedServiceId?: string;
  onChange: (serviceId: string | null) => void;
}

export default function ServiceCategoryFilter({
  services,
  selectedServiceId,
  onChange,
}: ServiceCategoryFilterProps) {
  return (
    <Select
      value={selectedServiceId || ""}
      onValueChange={(value) => onChange(value || null)}
    >
      <SelectTrigger>
        <SelectValue placeholder="All Services" />
      </SelectTrigger>
      <SelectContent>
        <SelectItem value="">All Services</SelectItem>
        {services.map((service) => (
          <SelectItem key={service.id} value={service.id}>
            {service.name}
          </SelectItem>
        ))}
      </SelectContent>
    </Select>
  
```

## `ServiceSelection.tsx`

```ts
// Path: components/complaints/ServiceSelection.tsx
"use client";

import { useEffect, useState } from "react";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Skeleton } from "@/components/ui/skeleton";

interface Service {
  id: string;
  name: string;
}

interface ServiceSelectionProps {
  entityId: string;
  entityType: string;
  selectedServiceId: string;
  onServiceSelect: (id: string) => void;
  providerCategory?: 'VAS' | 'BULK'; // Novi prop za tip provajdera
}

export function ServiceSelection({
  entityId,
  entityType,
  selectedServiceId,
  onServiceSelect,
  providerCategory, // Prihvatamo novi prop
}: ServiceSelectionProps) {
  const [services, setServices] = useState<Service[]>([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);
  const [currentEndpoint, setCurrentEndpoint] = useState<string | null>(null); // Dodatni state za debug

  useEffec
```

## `StatisticsCard.tsx`

```ts
// /components/complaints/StatisticsCard.tsx

import React from 'react';
import { Card, CardContent } from "@/components/ui/card";

interface StatisticsCardProps {
  title: string;
  value: number | string;
  icon?: React.ReactNode;
  description?: string;
  colorClass?: string;
  trend?: {
    direction: 'up' | 'down' | 'neutral';
    percentage: number;
  };
}

export const StatisticsCard: React.FC<StatisticsCardProps> = ({
  title,
  value,
  icon,
  description,
  colorClass = "bg-gray-50 border-gray-200",
  trend
}) => {
  return (
    <Card className={`${colorClass} border`}>
      <CardContent className="pt-6">
        <div className="flex justify-between items-start">
          <div>
            <h3 className="text-sm font-medium text-gray-500">{title}</h3>
            <div className="mt-1 flex items-baseline">
              <p className="text-2xl font-semibold text-gray-900">{value}</p>
              
              {trend && (
                <span className={`ml-2 flex items-
```

## `StatusBadge.tsx`

```ts
// /components/complaints/StatusBadge.tsx
import { ComplaintStatus } from "@prisma/client";
import { Badge } from "@/components/ui/badge";

interface StatusBadgeProps {
  status: ComplaintStatus;
}

export default function StatusBadge({ status }: StatusBadgeProps) {
  const getVariant = () => {
    switch (status) {
      case "NEW":
        return "bg-blue-100 text-blue-800 hover:bg-blue-100";
      case "ASSIGNED":
        return "bg-amber-100 text-amber-800 hover:bg-amber-100";
      case "IN_PROGRESS":
        return "bg-purple-100 text-purple-800 hover:bg-purple-100";
      case "PENDING":
        return "bg-orange-100 text-orange-800 hover:bg-orange-100";
      case "RESOLVED":
        return "bg-green-100 text-green-800 hover:bg-green-100";
      case "CLOSED":
        return "bg-gray-100 text-gray-800 hover:bg-gray-100";
      case "REJECTED":
        return "bg-red-100 text-red-800 hover:bg-red-100";
      default:
        return "bg-gray-100 text-gray-800 hover:bg-gray-100";

```

## `MonthlyComparisonChart.tsx`

```ts
// /components/complaints/charts/MonthlyComparisonChart.tsx


import { useMemo } from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer, Legend } from 'recharts';
import { Complaint } from '@prisma/client';
import { format, parseISO, subMonths, differenceInMonths, startOfMonth, endOfMonth } from 'date-fns';

interface MonthlyComparisonChartProps {
  complaints: Complaint[];
  monthsToShow?: number;
  title?: string;
  height?: number;
}

export function MonthlyComparisonChart({ 
  complaints, 
  monthsToShow = 6,
  title = "Monthly Complaint Comparison", 
  height = 300 
}: MonthlyComparisonChartProps) {
  const chartData = useMemo(() => {
    if (!complaints || complaints.length === 0) {
      return [];
    }

    // Get the current month and create an array of the last n months
    const today = new Date();
    const months = Array.from({ length: monthsToShow }, 
```

## `ProviderPerformance.tsx`

```ts
// /components/complaints/charts/ProviderPerformance.tsx


import { useMemo } from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer, Legend, LabelList } from 'recharts';
import { Complaint, Provider } from '@prisma/client';

interface ProviderPerformanceProps {
  complaints: (Complaint & {
    provider?: Provider | null;
  })[];
  title?: string;
  height?: number;
  maxProviders?: number;
}

export function ProviderPerformance({ 
  complaints, 
  title = "Provider Performance", 
  height = 400,
  maxProviders = 10
}: ProviderPerformanceProps) {
  const chartData = useMemo(() => {
    if (!complaints || complaints.length === 0) {
      return [];
    }

    // Group complaints by provider
    const providerComplaints: Record<string, {
      providerId: string,
      providerName: string,
      total: number,
      resolved: number,
      pending: number,
      avgR
```

## `ServiceCategoryBreakdown.tsx`

```ts
// /components/complaints/charts/ServiceCategoryBreakdown.tsx



import { useMemo } from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { PieChart, Pie, Cell, ResponsiveContainer, Legend, Tooltip } from 'recharts';
import { Complaint, Service, ServiceType } from '@prisma/client';

interface ServiceCategoryBreakdownProps {
  complaints: (Complaint & {
    service?: Service | null;
  })[];
  title?: string;
  height?: number;
}

export function ServiceCategoryBreakdown({ 
  complaints, 
  title = "Complaints by Service Category", 
  height = 300 
}: ServiceCategoryBreakdownProps) {
  const chartData = useMemo(() => {
    if (!complaints || complaints.length === 0) {
      return [];
    }

    // Group complaints by service type
    const serviceTypes: Record<string, number> = {
      VAS: 0,
      BULK: 0,
      HUMANITARIAN: 0,
      PARKING: 0,
      UNKNOWN: 0
    };

    complaints.forEach(complaint => {
      if (complaint.service) 
```

## `StatusChart.tsx`

```ts
// components/complaints/charts/StatusChart.tsx
"use client";

import { useState, useEffect } from "react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { PieChart, Pie, Cell, ResponsiveContainer, Legend, Tooltip } from "recharts";
import { ComplaintStatus } from "@/lib/types/enums";

interface StatusChartProps {
  data?: { status: ComplaintStatus; count: number }[];
  isLoading?: boolean;
  title?: string;
}

const STATUS_COLORS = {
  [ComplaintStatus.NEW]: "#0ea5e9", // sky-500
  [ComplaintStatus.ASSIGNED]: "#8b5cf6", // violet-500
  [ComplaintStatus.IN_PROGRESS]: "#f59e0b", // amber-500
  [ComplaintStatus.PENDING]: "#6b7280", // gray-500
  [ComplaintStatus.RESOLVED]: "#10b981", // emerald-500
  [ComplaintStatus.CLOSED]: "#64748b", // slate-500
  [ComplaintStatus.REJECTED]: "#ef4444", // red-500
};

const STATUS_LABELS = {
  [ComplaintStatus.NEW]: "New",
  [ComplaintStatus.ASSIGNED]: "Assigned",
  [ComplaintStatus.IN_PROGRESS]: "In Progress"
```

## `TrendChart.tsx`

```ts
// components/complaints/charts/TrendChart.tsx
"use client";

import { useState, useEffect } from "react";
import { Card, CardContent, CardHeader, CardTitle, CardDescription } from "@/components/ui/card";
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from "recharts";
import { ComplaintStatus } from "@/lib/types/enums";

interface TrendDataPoint {
  date: string;
  new: number;
  resolved: number;
  closed: number;
  total: number;
}

interface TrendChartProps {
  data?: TrendDataPoint[];
  isLoading?: boolean;
  period?: "week" | "month" | "quarter" | "year";
  title?: string;
}

export function TrendChart({ 
  data = [], 
  isLoading = false,
  period = "month",
  title = "Complaint Trends"
}: TrendChartProps) {
  const [periodDescription, setPeriodDescription] = useState("last 30 days");

  useEffect(() => {
    switch (period) {
      case "week":
        setPeriodDescription("last 7 days");
        break;
      case "month":
        s
```

## `ExcelExport.tsx`

```ts
// /components/complaints/reports/ExcelExport.tsx


import { useState } from 'react';
import { Button } from '@/components/ui/button';
import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from '@/components/ui/card';
import { Checkbox } from '@/components/ui/checkbox';
import { Label } from '@/components/ui/label';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';
import { FileDown, Loader2 } from 'lucide-react';
import { exportComplaints } from '@/actions/complaints/export';

interface ExcelExportProps {
  onExportStart?: () => void;
  onExportComplete?: (url: string) => void;
  onExportError?: (error: string) => void;
}

export function ExcelExport({ 
  onExportStart, 
  onExportComplete,
  onExportError 
}: ExcelExportProps) {
  const [loading, setLoading] = useState(false);
  const [exportType, setExportType] = useState<'all' | 'filtered'>('filtered');
  const [includeOptions, setIncludeOptions] = use
```

## `KpiDashboard.tsx`

```ts
// components/complaints/reports/KpiDashboard.tsx
"use client";

import { useEffect, useState } from "react";
import { Card, CardContent, CardHeader, CardTitle, CardDescription } from "@/components/ui/card";
import { StatusChart } from "@/components/complaints/charts/StatusChart";
import { TrendChart } from "@/components/complaints/charts/TrendChart";
import { MonthlyComparisonChart } from "@/components/complaints/charts/MonthlyComparisonChart";
import { ServiceCategoryBreakdown } from "@/components/complaints/charts/ServiceCategoryBreakdown";
import { ProviderPerformance } from "@/components/complaints/charts/ProviderPerformance";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { DateRange } from "@/components/ui/date-range";
import { Button } from "@/components/ui/button";
import { Loader2, FileDown } from "lucide-react";
import { ComplaintStatus, ServiceType } from "@/lib/types/enums";
import { format, addDays, subDays, subMonths } from "date-
```

## `AttachmentList.tsx`

```ts
// components/contracts/AttachmentList.tsx
"use client";
import { FileIcon, DownloadIcon, TrashIcon } from "lucide-react";
import { Button } from "@/components/ui/button";
import { deleteAttachment } from "@/actions/contracts/delete-attachment";
import { toast } from "sonner";
import { useState } from "react";

interface Attachment {
  id: string;
  name: string;
  fileUrl: string;
  fileType: string;
  uploadedAt: Date;
  uploadedBy: {
    name: string;
  };
}

interface AttachmentListProps {
  contractId: string;
  attachments: Attachment[];
  onDelete?: (attachmentId: string) => void;
}

export function AttachmentList({ contractId, attachments, onDelete }: AttachmentListProps) {

  const [deletingId, setDeletingId] = useState<string | null>(null);

  const handleDelete = async (attachmentId: string) => {
    setDeletingId(attachmentId);
    try {
      const result = await deleteAttachment(contractId, attachmentId);
      if (result.success) {
        toast({
          title: "Attac
```

## `AttachmentUpload.tsx`

```ts
///components/contracts/AttachmentUpload.tsx

"use client";

import { useState } from "react";
import { Upload, X } from "lucide-react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { addAttachment } from "@/actions/contracts/add-attachment";
import { toast } from "sonner";

interface AttachmentUploadProps {
  contractId: string;
  onUploadComplete?: () => void;
}

export function AttachmentUpload({ contractId, onUploadComplete }: AttachmentUploadProps) {
  const [file, setFile] = useState<File | null>(null);
  const [uploading, setUploading] = useState(false);


  const handleFileChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    if (e.target.files && e.target.files.length > 0) {
      setFile(e.target.files[0]);
    }
  };

  const handleRemoveFile = () => {
    setFile(null);
  };

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    if 
```

## `ContractDetails.tsx`

```ts
///components/contracts/ContractDetails.tsx

"use client";

import { useState } from "react";
import Link from "next/link";
import { useRouter } from "next/navigation";
import { formatDate } from "@/lib/utils";
import { ContractStatusBadge } from "@/components/contracts/ContractStatusBadge";
import { ExpiryWarning } from "@/components/contracts/ExpiryWarning";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { AttachmentList } from "@/components/contracts/AttachmentList";
import { ReminderForm } from "@/components/contracts/ReminderForm";
import { Contract, ContractAttachment, ContractReminder, Service } from "@prisma/client";

interface ContractWithRelations extends Contract {
  provider?: { name: string; id: string } | null;
  humanitarianOrg?: { name: string; id: string } | null;
  parkingService?: { name: string; id
```

## `ContractFilters.tsx`

```ts
// /components/contracts/ContractFilters.tsx
"use client";

import { useState, useEffect, useCallback } from "react";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Card, CardContent } from "@/components/ui/card";
import { ContractType, ContractStatus } from "@prisma/client";

interface Contract {
  id: string;
  name: string;
  contractNumber: string;
  type: ContractType;
  status: ContractStatus;
  startDate: Date;
  endDate: Date;
  revenuePercentage: number;
  provider?: { id: string; name: string } | null;
  humanitarianOrg?: { id: string; name: string } | null;
  parkingService?: { id: string; name: string } | null;
  createdAt: Date;
}

interface ContractFiltersProps {
  contracts: Contract[] | undefined | null;
  onFilterChange: (filtered: Contract[]) => void;
  serverTime?: string; 
}

export function Cont
```

## `ContractForm.tsx`

```ts
"use client";

"use client";

import { useState, useEffect } from "react";
import { useRouter } from "next/navigation";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { createContract } from "@/actions/contracts/create";
import { updateContract } from "@/actions/contracts/update";
import { ServiceSelector } from "@/components/contracts/ServiceSelector";
import { ContractType } from "@prisma/client";
import { contractSchema } from "@/schemas/contract";
import { ContractFormData, SelectedService } from "@/lib/types/contract-types";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Textarea } from "@/components/ui/textarea";
import { Form, FormControl, FormField, FormItem, FormLabel, FormMessage } from "@/components/ui/form";
import { Card, CardHeader, CardTitle, CardContent,
```

## `ContractList.tsx`

```ts
// Path: /components/contracts/ContractsList.tsx
"use client";

import { memo, useMemo } from "react";
import { Contract } from "@/lib/types/contract-types";
import { ContractStatus } from "@prisma/client";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { Badge } from "@/components/ui/badge";
import { Button } from "@/components/ui/button";
import { formatDate } from "@/lib/utils";
import { 
  Eye, 
  Edit, 
  FileText, 
  Calendar, 
  Users, 
  DollarSign,
  AlertTriangle,
  CheckCircle,
  Clock,
  XCircle
} from "lucide-react";
import Link from "next/link";

interface ContractListProps {
  contracts: Contract[];
  serverTime: string;
}

// Status badge component
const StatusBadge = memo(({ status }: { status: ContractStatus }) => {
  const getStatusConfig = (status: ContractStatus) => {
    switch (status) {
      case ContractStatus.ACTIVE:
        return {
          variant: "default" as const,
          cl
```

## `ContractsSection.tsx`

```ts
// Path: /components/contracts/ContractsSection.tsx
"use client";

import { useReducer, useEffect, useCallback, useMemo, useRef } from "react";
import { useRouter, useSearchParams } from "next/navigation";
import { ContractFilters } from "@/components/contracts/ContractFilters";
import { ContractsList} from "@/components/contracts/ContractList";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { ChevronLeft, ChevronRight, Download } from "lucide-react";
import { Contract } from "@/lib/types/contract-types";
import { ContractStatus } from "@prisma/client"; // Dodato za StatusBadge

const ITEMS_PER_PAGE_OPTIONS = [10, 25, 50, 100];

// State interface
interface ContractsState {
  contracts: Contract[];
  filteredContracts: Contract[];
  totalCount: number;
  totalPages: number;
  currentPage: number
```

## `ContractStatusBadge.tsx`

```ts
///components/contracts/ContractStatusBadge.tsx

import { Badge } from "@/components/ui/badge";
import { ContractStatus } from "@prisma/client";

interface ContractStatusBadgeProps {
  status: ContractStatus;
}

export function ContractStatusBadge({ status }: ContractStatusBadgeProps) {
  const getStatusConfig = (status: ContractStatus) => {
    switch (status) {
      case "ACTIVE":
        return {
          label: "Active",
          variant: "success" as const,
          className: "bg-green-100 text-green-800 hover:bg-green-100"
        };
      case "EXPIRED":
        return {
          label: "Expired",
          variant: "destructive" as const,
          className: "bg-red-100 text-red-800 hover:bg-red-100"
        };
      case "PENDING":
        return {
          label: "Pending",
          variant: "warning" as const,
          className: "bg-yellow-100 text-yellow-800 hover:bg-yellow-100"
        };
      case "RENEWAL_IN_PROGRESS":
        return {
          label: "Renew
```

## `ContractStatusManager.tsx`

```ts
// components/contracts/ContractStatusManager.tsx

"use client";

import React, { useState } from 'react';
import { ContractStatus } from '@prisma/client';
import { Button } from '@/components/ui/button';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';
import { Textarea } from '@/components/ui/textarea';
import { Label } from '@/components/ui/label';
import { Dialog, DialogContent, DialogDescription, DialogFooter, DialogHeader, DialogTitle, DialogTrigger } from '@/components/ui/dialog';
import { Badge } from '@/components/ui/badge';
import { AlertCircle, CheckCircle, Clock, FileText, RefreshCw, Edit, Loader2 } from 'lucide-react';
import { useContracts } from '@/hooks/use-contracts';
import { useRouter } from 'next/navigation';
import { toast } from 'sonner';

interface Contract {
  id: string;
  name: string;
  contractNumber: string;
  type: 'HUMANITARIAN' | 'PROVIDER' | 'PARKING';
  status: ContractStatus;
  humanitarianOrg?: {
```

## `enhanced-contract-list.tsx`

```ts
// components/contracts/enhanced-contract-list.tsx
"use client";
import { useState, useEffect } from "react";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { Badge } from "@/components/ui/badge";
import { Button } from "@/components/ui/button";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";
import { formatDate } from "@/lib/utils";
import Link from "next/link";
import { MoreHorizontal, AlertTriangle, Calendar, FileText } from "lucide-react";
import { RenewalDialog } from "./renewal-dialog";
import { StatusChangeDialog } from "./status-change-dialog";

interface ContractProvider {
  id: string;
  name: string;
}

interface ContractRenewal {
  id: string;
  contractId: string;
  subStatus: string;
  renewalStartDate?: string | Date;
  proposedStartDate?: string | Date;
  proposedEndDate?: string | Date;
  createdAt: string | Date
```

## `ExpiryWarning.tsx`

```ts
///components/contracts/ExpiryWarning.tsx


"use client";

import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert";
import { AlertCircle } from "lucide-react";
import { Button } from "@/components/ui/button";
import { useRouter } from "next/navigation";

interface ExpiryWarningProps {
  endDate: Date;
  contractId: string;
}

export function ExpiryWarning({ endDate, contractId }: ExpiryWarningProps) {
  const router = useRouter();
  
  const daysUntilExpiration = () => {
    const today = new Date();
    const end = new Date(endDate);
    const diffTime = end.getTime() - today.getTime();
    return Math.ceil(diffTime / (1000 * 60 * 60 * 24));
  };
  
  const days = daysUntilExpiration();
  
  return (
    <Alert variant="warning" className="bg-amber-50 border-amber-200">
      <AlertCircle className="h-4 w-4" />
      <AlertTitle>Contract Expiring Soon</AlertTitle>
      <AlertDescription className="flex justify-between items-center">
        <span>This contract wil
```

## `ReminderForm.tsx`

```ts
///components/contracts/ReminderForm.tsx


"use client";

import { useState } from "react";
import { CalendarIcon, Plus, Bell } from "lucide-react";
import { Button } from "@/components/ui/button";
import { Calendar } from "@/components/ui/calendar";
import {
  Popover,
  PopoverContent,
  PopoverTrigger,
} from "@/components/ui/popover";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { Label } from "@/components/ui/label";
import { cn, formatDate } from "@/lib/utils";
import { createReminder } from "@/actions/contracts/create-reminder";
import { acknowledgeReminder } from "@/actions/contracts/acknowledge-reminder";
import { toast } from "sonner";

interface Reminder {
  id: string;
  reminderDate: Date;
  reminderType: string;
  isAcknowledged: boolean;
  acknowledgedBy?: {
    name: string;
  } | null;
}

interface ReminderFormProps {
  contractId: string;
  endDate: Date;
  reminders: Reminder[];
  onReminder
```

## `renewal-dialog.tsx`

```ts
// /components/contracts/renewal-dialog.tsx
"use client";
import { useState, useEffect } from "react";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import * as z from "zod";
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogFooter,
} from "@/components/ui/dialog";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Textarea } from "@/components/ui/textarea";
import { Badge } from "@/components/ui/badge";
import { Separator } from "@/components/ui/separator";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Checkbox } from "@/components/ui/checkbox";
import { Label } from "@/components/ui/la
```

## `renewal-substatus-dialog.tsx`

```ts
// /components/contracts/renewal-substatus-dialog.tsx
"use client";
import { useState, useEffect } from "react";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import * as z from "zod";
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogFooter,
} from "@/components/ui/dialog";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { Button } from "@/components/ui/button";
import { Textarea } from "@/components/ui/textarea";
import { Badge } from "@/components/ui/badge";
import { 
  AlertTriangle, 
  RefreshCw, 
  FileText, 
  Scale, 
  DollarSign, 
  PenTool, 
  CheckCircle, 
  Settings, 
  UserCheck 
} from "lucide-react";
import { toast } from "sonner";
import { ContractRenewalSubStatus } from "@prisma/client";
import { updateRene
```

## `RevenueBreakdown.tsx`

```ts
// Path: /components/contracts/RevenueBreakdown.tsx
"use client";

import { useState, useEffect } from "react";
import { 
  DollarSign, 
  TrendingUp, 
  PieChart, 
  Users, 
  ParkingCircle, 
  Calendar,
  MessageSquare,
  Database,
  ChevronDown,
  ChevronUp
} from "lucide-react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Progress } from "@/components/ui/progress";
import { ContractType } from "@prisma/client";
import { Skeleton } from "@/components/ui/skeleton";
import { Button } from "@/components/ui/button";
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table";

interface ServiceBreakdownItem {
  id: string;
  name: string;
  revenueAmount: number;
  percentage: number;
  details?: {
    messages: number;
    messageRevenue: number;
    records: number;
    recordRevenue: number;
    monthlyBreakdown?: {
      month: string;
      messages: number;
      messageRate: number;
      mess
```

## `ServiceLinkToContractModal.tsx`

```ts
//components/contracts/ServiceLinkToContractModal.tsx

"use client";

import React, { useState, useEffect } from "react";
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
} from "@/components/ui/dialog";
import { Button } from "@/components/ui/button";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { Label } from "@/components/ui/label";
import { Loader2 } from "lucide-react";
import { toast } from "sonner";

import { getProviderContractsForLinking } from "@/actions/contracts/getProviderContractsForLinking";
import { linkServiceToContract } from "@/actions/contracts/linkServiceToContract";

interface ServiceLinkToContractModalProps {
  isOpen: boolean;
  onClose: () => void;
  serviceId: string;
  serviceName: string;
  providerId: string;
  onLinkSuccess: () => void; // Callback after successful linking
}

interface ContractOption {
  id: string;
  name: st
```

## `ServiceSelector.tsx`

```ts
// components/contracts/ServiceSelector.tsx
"use client";

import { useState, useEffect } from "react";
import { ContractType, ServiceType } from "@prisma/client";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Label } from "@/components/ui/label";
import { useToast } from "@/components/toast/toast-context";
import { XCircle } from "lucide-react";
import { getServicesByType } from '@/actions/services/get';

interface Service {
  id: string;
  name: string;
  type: ServiceType;
}

interface SelectedService {
  serviceId: string;
  specificTerms?: string;
}

interface ServiceSelectorProps {
  contractId?: string;
  contractType: ContractType;
  selectedServices: SelectedService[];
  onChange: (services: SelectedService[]) => void;
  readOnly?: boolean;
  error?: string;
  disabled?: boolean;
}

export function ServiceSelector({

```

## `status-change-dialog.tsx`

```ts
// /components/contracts/status-change-dialog.tsx
"use client";
import { useState, useEffect } from "react";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import * as z from "zod";
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogFooter,
} from "@/components/ui/dialog";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { Button } from "@/components/ui/button";
import { Textarea } from "@/components/ui/textarea";
import { Badge } from "@/components/ui/badge";
import { AlertTriangle, CheckCircle, XCircle, Clock, RefreshCw } from "lucide-react";
import { toast } from "sonner";
import { ContractStatus } from "@/lib/types/contract-types";
import { updateContractStatus } from "@/actions/contracts/contract-actions";

const statusCh
```

## `HumanitarianContractCard.tsx`

```ts
///components/contracts/category/HumanitarianContractCard.tsx


"use client";

import { useState } from "react";
import Link from "next/link";
import { format } from "date-fns";
import { ContractStatusBadge } from "@/components/contracts/ContractStatusBadge";
import { ExpiryWarning } from "@/components/contracts/ExpiryWarning";
import { HumanitarianRenewalSubStatus } from "@prisma/client";

interface HumanitarianContractCardProps {
  contract: {
    id: string;
    name: string;
    contractNumber: string;
    status: string;
    startDate: Date;
    endDate: Date;
    revenuePercentage: number;
    description?: string;
    humanitarianOrg?: {
      id: string;
      name: string;
      contactName?: string;
      email?: string;
      phone?: string;
    } | null;
    humanitarianRenewals?: {
      id: string;
      subStatus: HumanitarianRenewalSubStatus;
      renewalStartDate: Date;
      proposedStartDate: Date;
      proposedEndDate: Date;
      proposedRevenue: number;
      do
```

## `ParkingContractCard.tsx`

```ts
///components/contracts/category/ParkingContractCard.tsx


"use client";

import { useState } from "react";
import Link from "next/link";
import { format } from "date-fns";
import { ContractStatusBadge } from "@/components/contracts/ContractStatusBadge";
import { ExpiryWarning } from "@/components/contracts/ExpiryWarning";

interface ParkingContractCardProps {
  contract: {
    id: string;
    name: string;
    contractNumber: string;
    status: string;
    startDate: Date;
    endDate: Date;
    revenuePercentage: number;
    description?: string;
    parkingService?: {
      id: string;
      name: string;
      description?: string;
      contactName?: string;
      email?: string;
      phone?: string;
    } | null;
    services?: {
      serviceId: string;
      service: {
        id: string;
        name: string;
        type: string;
      };
    }[];
  };
}

export function ParkingContractCard({ contract }: ParkingContractCardProps) {
  const [isExpanded, setIsExpanded] = useS
```

## `ProviderContractCard.tsx`

```ts
///components/contracts/category/ProviderContractCard.tsx


"use client";

import { Card, CardContent, CardFooter, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { ContractStatusBadge } from "@/components/contracts/ContractStatusBadge";
import { ExpiryWarning } from "@/components/contracts/ExpiryWarning";
import { formatDate } from "@/lib/utils";
import { useRouter } from "next/navigation";
import { ContractStatus } from "@prisma/client";

interface Provider {
  id: string;
  name: string;
  contactName?: string | null;
  email?: string | null;
  phone?: string | null;
}

interface ProviderContractCardProps {
  id: string;
  name: string;
  contractNumber: string;
  status: ContractStatus;
  startDate: Date;
  endDate: Date;
  revenuePercentage: number;
  provider: Provider;
  servicesCount: number;
}

export function ProviderContractCard({
  id,
  name,
  contractNumber,
  status,
  startDate,
  endDate,
  revenuePercentage
```

## `ContractTypeDistribution.tsx`

```ts
// /components/contracts/charts/ContractTypeDistribution.tsx
'use client';

import React from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'; // Pretpostavljena putanja do Shadcn UI Card komponente
import { ContractType } from '@prisma/client'; // Uvoz Prisma enum-a

// Tip za podatke koji se prosleđuju komponenti
interface ContractTypeDistributionData {
    type: ContractType;
    count: number;
}

interface ContractTypeDistributionProps {
    // Podaci za prikaz, formatirani kao niz objekata { type: COUNT }
    // Ili možete prihvatiti agregirani objekat { PROVIDER: 10, HUMANITARIAN: 5, ... }
    data: ContractTypeDistributionData[]; // Odabrali smo format niza
}

/**
 * Komponenta koja prikazuje grafikon distribucije ugovora po tipu.
 * @param data - Podaci o broju ugovora po svakom tipu.
 */
export function ContractTypeDistribution({ data }: ContractTypeDistributionProps) {
    // 1. Priprema podataka za grafikon
    // Format podataka zavi
```

## `ExpiryTimelineChart.tsx`

```ts
///components/contracts/charts/ExpiryTimelineChart.tsx
"use client";

import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { ContractType } from "@prisma/client";
import {
  BarChart,
  Bar,
  XAxis,
  YAxis,
  CartesianGrid,
  Tooltip,
  Legend,
  ResponsiveContainer,
  TooltipProps,
} from "recharts";
import { format } from "date-fns";

interface ContractInfo {
  id: string;
  contractNumber: string;
  organizationName: string;
  type: ContractType;
  endDate: Date;
  status: string;
  value?: number;
  hasRenewal: boolean;
}

interface ExpiryData {
  month: string;
  date: Date;
  provider: number;
  humanitarian: number;
  parking: number;
  total: number;
  contracts: ContractInfo[];
}

interface ExpiryTimelineChartProps {
  data: ExpiryData[];
  title?: string;
  showLegend?: boolean;
  height?: number;
}

export function ExpiryTimelineChart({ 
  data = [],
  title = "Vremenski tok isteka ugovora",
  showLegend = true,
  height = 350
}: ExpiryTi
```

## `RevenueProjection.tsx`

```ts
// /components/contracts/charts/RevenueProjection.tsx
'use client';

import React from 'react';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'; // Pretpostavljena putanja do Shadcn UI Card komponente
import { Skeleton } from '@/components/ui/skeleton'; // Pretpostavljena putanja do Shadcn UI Skeleton komponente

// Tip za podatke koji se prosleđuju komponenti za projekciju prihoda
interface RevenueProjectionDataPoint {
    period: string; // Npr. "Jan 2024", "Q1 2025"
    revenue: number; // Projektovani ili obračunati prihod za taj period
}

interface RevenueProjectionProps {
    // Podaci za prikaz, niz tačaka (period, prihod)
    data: RevenueProjectionDataPoint[];
    loading?: boolean; // Opciono: indikator učitavanja
    error?: Error | null; // Opciono: objekat greške
}

/**
 * Komponenta koja prikazuje grafikon projekcije prihoda od ugovora.
 * @param data - Podaci o prihodima po periodima.
 * @param loading - Indikator učitavanja podataka.
 * @
```

## `ContractKpiDashboard.tsx`

```ts
// /components/contracts/reports/ContractKpiDashboard.tsx
'use client';

import React, { useState, useEffect } from 'react';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card'; // Shadcn UI Card
import { Skeleton } from '@/components/ui/skeleton'; // Shadcn UI Skeleton
import { useContracts } from '@/hooks/use-contracts'; // Hook za dohvatanje ugovora (već postoji)
import { useExpiringContracts } from '@/hooks/use-expiring-contracts'; // Hook za istekle ugovore (već postoji)
import { useContractReminders } from '@/hooks/use-contract-reminders'; // Hook za podsetnike (kreirali smo)
import { calculateTotalPlatformRevenue, calculateContractRevenue } from '@/lib/contracts/revenue-calculator'; // Utility za kalkulaciju prihoda (kreirali smo)
import { ContractStatus, ContractType } from '@prisma/client'; // Prisma enumi
import { getContractStatusLabel } from '@/lib/types/contract-status'; // Utility za labele statusa (kreirali smo)

// Import grafikona (kreirali 
```

## `DashboardHeader.tsx`

```ts
// components/dashboard/DashboardHeader.tsx

// Path: components/dashboard/DashboardHeader.tsx
import React from 'react';

interface DashboardHeaderProps {
  heading: string;
  text?: string;
  children?: React.ReactNode;
}

// Komponenta za zaglavlje stranice unutar dashboard-a
export default function DashboardHeader({ heading, text, children }: DashboardHeaderProps) {
  return (
    <div className="flex items-center justify-between px-2">
      <div className="grid gap-1">
        <h1 className="font-heading text-3xl md:text-4xl">
          {heading}
        </h1>
        {text && (
          <p className="text-lg text-muted-foreground">
            {text}
          </p>
        )}
      </div>
      {children} {/* Ovde se renderuju dugmad ili drugi elementi */}
    </div>
  );
}

```

## `DashboardShell.tsx`

```ts
// components/dashboard/DashboardShell.tsx


// Path: components/dashboard/DashboardShell.tsx
import React from 'react';
import { cn } from "@/lib/utils"; // Pretpostavljamo da imate utility funkciju za spajanje klasa

interface DashboardShellProps extends React.HTMLAttributes<HTMLDivElement> {}

// Komponenta za osnovni layout strukturu stranice unutar dashboard-a
export default function DashboardShell({ children, className, ...props }: DashboardShellProps) {
  return (
    <div className={cn("grid items-start gap-8", className)} {...props}>
      {children}
    </div>
  );
}

```

## `HumanitarianOrgContracts.tsx`

```ts
// /components/humanitarian-orgs/HumanitarianOrgContracts.tsx
"use client";

import React, { useState, useEffect, useCallback } from "react";
import { useRouter } from "next/navigation";
import Link from "next/link";
import { Button } from "@/components/ui/button";
import { Badge } from "@/components/ui/badge";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { formatDate } from "@/lib/utils"; // Pretpostavljamo da formatDate postoji
import { Pagination } from "@/components/ui/pagination";
import { AlertCircle, Clock, FileText, Loader2 } from "lucide-react";

interface Contract {
  id: string;
  name: string;
  contractNumber: string;
  status: "ACTIVE" | "EXPIRED" | "PENDING" | "RENEWAL_IN_PROGRESS";
  startDate: string | Date;
  endDate: string | Date;
  revenuePercentage: number;
  type: "HUMANITARIAN";
  // Dodajte ovde polja koja su uključena u API ruti, npr:
  // operator: { id: string; name: string; };
}

interface HumanitarianOrgContractsP
```

## `HumanitarianOrgDetails.tsx`

```ts
// /components/humanitarian-orgs/HumanitarianOrgDetails.tsx
"use client";
import React from 'react';
import { HumanitarianOrgWithDetails } from '@/lib/types/humanitarian-org-types';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { Badge } from "@/components/ui/badge";

interface HumanitarianOrgDetailsProps {
    organization: HumanitarianOrgWithDetails;
}
export function formatDate(date: Date): string {
  return new Intl.DateTimeFormat('sr-RS', {
    year: 'numeric',
    month: '2-digit',
    day: '2-digit',
    hour: '2-digit',
    minute: '2-digit'
  }).format(new Date(date));
}

export function HumanitarianOrgDetails({ organization }: HumanitarianOrgDetailsProps) {
    if (!organization) {
        return <div>No organization data available.</div>;
    }

    return (
  <div className="space-y-6">
    <Card>
      <CardHeader>
        <CardTitle className="text-xl font-bold border-b">Basic Information</CardTitle>
      </CardHeader>
      <Car
```

## `HumanitarianOrgFilters.tsx`

```ts
// Path: components/humanitarian-orgs/HumanitarianOrgFilters.tsx
"use client";

import { useState, useEffect, useCallback } from "react";
import { HumanitarianOrgFilterOptions } from "@/lib/types/humanitarian-org-types";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Label } from "@/components/ui/label";
import { RadioGroup, RadioGroupItem } from "@/components/ui/radio-group";
import { Select, SelectTrigger, SelectValue, SelectContent, SelectItem } from "@/components/ui/select";
import { Checkbox } from "@/components/ui/checkbox";

interface HumanitarianOrgFiltersProps {
  onFilterChange: (filters: HumanitarianOrgFilterOptions) => void;
  initialFilters: HumanitarianOrgFilterOptions;
}

export function HumanitarianOrgFilters({ onFilterChange, initialFilters }: HumanitarianOrgFiltersProps) {
  const [localFilters, setLocalFilters] = useState<HumanitarianOrgFilterOptions>(initialFilters);
  const [searchInput, setSearchInput] = us
```

## `HumanitarianOrgForm.tsx`

```ts
// components/humanitarian-orgs/HumanitarianOrgForm.tsx
"use client";

import { useState } from "react";
import { useRouter } from "next/navigation";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { humanitarianOrgSchema, HumanitarianOrgFormData } from "@/schemas/humanitarian-org";
import { createHumanitarianOrg } from "@/actions/humanitarian-orgs/create";
import { updateHumanitarianOrg } from "@/actions/humanitarian-orgs/update";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Checkbox } from "@/components/ui/checkbox";
import { Label } from "@/components/ui/label";
import { Textarea } from "@/components/ui/textarea";
import { toast } from "sonner";
import { Form, FormControl, FormField, FormItem, FormLabel, FormMessage } from "@/components/ui/form";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Separator } from "@/components/ui
```

## `HumanitarianOrgList.tsx`

```ts
// Path: components/humanitarian-orgs/HumanitarianOrgList.tsx
"use client";

import { useState, useEffect, useCallback } from "react";
import Link from "next/link";
import { useRouter, useSearchParams } from "next/navigation";
import { useHumanitarianOrgs } from "@/hooks/use-humanitarian-orgs";
import { HumanitarianOrgFilters } from "@/components/humanitarian-orgs/HumanitarianOrgFilters";
import { HumanitarianOrgFilterOptions, PaginationOptions } from "@/lib/types/humanitarian-org-types";
import { Button } from "@/components/ui/button";
import {
  Pagination,
  PaginationContent,
  PaginationItem,
  PaginationLink,
  PaginationNext,
  PaginationPrevious,
} from "@/components/ui/pagination";
import { Card, CardContent, CardFooter, CardHeader, CardTitle } from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import { Skeleton } from "@/components/ui/skeleton";

export function HumanitarianOrgList() {
  const router = useRouter();
  const searchParams = useSearchPara
```

## `LogFilters.tsx`

```ts
///components/logs/LogFilters.tsx

"use client";

import React, { useState, useEffect } from 'react';
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { LogActionType, LogStatus } from "@prisma/client";
import { format } from "date-fns";
import { CalendarIcon, Search, XCircle } from "lucide-react";
import { Popover, PopoverContent, PopoverTrigger } from "@/components/ui/popover";
import { Calendar } from "@/components/ui/calendar";
import { cn } from "@/lib/utils";
import { Card, CardContent, CardHeader } from "@/components/ui/card"; // Added Card imports


interface LogFiltersState {
    action?: LogActionType | 'ALL';
    status?: LogStatus | 'ALL';
    subjectKeyword?: string;
    dateFrom?: Date;
    dateTo?: Date;
}

interface LogFiltersProps {
    initialFilters?: LogFiltersStat
```

## `AdminNotificationControls.tsx`

```ts
///components/notifications/AdminNotificationControls.tsx


"use client";

import { useState } from "react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { NotificationType } from "@prisma/client";
import { toast } from "sonner";

type AdminNotificationControlsProps = {
  userRoles: string[];
};

export default function AdminNotificationControls({ userRoles }: AdminNotificationControlsProps) {
  const [notificationType, setNotificationType] = useState<NotificationType>(NotificationType.SYSTEM);
  const [title, setTitle] = useState("");
  const [message, setMessage] = useState("");
  cons
```

## `AlertBanner.tsx`

```ts
// /components/notifications/AlertBanner.tsx
import { useState, useEffect } from 'react';
import { XCircle, AlertTriangle, Info, CheckCircle } from 'lucide-react';
import { cn } from '@/lib/utils';

type AlertType = 'success' | 'info' | 'warning' | 'error';

interface AlertBannerProps {
  message: string;
  type?: AlertType;
  showIcon?: boolean;
  dismissible?: boolean;
  duration?: number | null; // null means it won't auto-dismiss
  className?: string;
  onDismiss?: () => void;
}

export const AlertBanner = ({
  message,
  type = 'info',
  showIcon = true,
  dismissible = true,
  duration = 5000, // 5 seconds by default
  className = '',
  onDismiss
}: AlertBannerProps) => {
  const [visible, setVisible] = useState(true);
  
  useEffect(() => {
    if (duration !== null && visible) {
      const timer = setTimeout(() => {
        setVisible(false);
        if (onDismiss) onDismiss();
      }, duration);
      
      return () => clearTimeout(timer);
    }
  }, [duration, visible, on
```

## `EmailPreview.tsx`

```ts
//components/notifications/EmailPreview.tsx

"use client";

import { useState } from "react";
import { X, Maximize, Minimize, Send, Eye } from "lucide-react";
import {
  Dialog,
  DialogContent,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog";
import { Button } from "@/components/ui/button";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";

interface EmailPreviewProps {
  subject: string;
  htmlContent: string;
  textContent: string;
  recipients: string[];
  onSend?: () => Promise<void>;
  canSend?: boolean;
}

export default function EmailPreview({
  subject,
  htmlContent,
  textContent,
  recipients,
  onSend,
  canSend = false,
}: EmailPreviewProps) {
  const [isLoading, setIsLoading] = useState(false);
  const [isFullScreen, setIsFullScreen] = useState(false);
  
  const handleSend = async () => {
    if (!onSend) return;
    
    setIsLoading(true);
    try {
      await onSend();
    } catch (error)
```

## `NotificationBadge.tsx`

```ts
///components/notifications/NotificationBadge.tsx

"use client";

import { useState, useEffect } from "react";
import { Bell } from "lucide-react";
import {
  Popover,
  PopoverContent,
  PopoverTrigger,
} from "@/components/ui/popover";
import NotificationList from "./NotificationList";
import { Notification } from "@/lib/types/notification-types";

interface NotificationBadgeProps {
  initialCount?: number;
  initialNotifications?: Notification[];
  fetchNotifications?: () => Promise<Notification[]>;
}

export default function NotificationBadge({
  initialCount = 0,
  initialNotifications = [],
  fetchNotifications,
}: NotificationBadgeProps) {
  const [isOpen, setIsOpen] = useState(false);
  const [unreadCount, setUnreadCount] = useState(initialCount);
  const [notifications, setNotifications] = useState<Notification[]>(initialNotifications);
  const [isLoading, setIsLoading] = useState(false);

  const loadNotifications = async () => {
    if (!fetchNotifications) return;
    
    
```

## `NotificationList.tsx`

```ts
///components/notifications/NotificationList.tsx 

"use client";

import { useState, useEffect } from "react";
import { useRouter } from "next/navigation";
import { formatDistanceToNow } from "date-fns";
import { Bell, CheckCircle, AlertTriangle, Info, Calendar, MessageSquare } from "lucide-react";
import { Notification, NotificationType } from "@/lib/types/notification-types";
import { markAsRead } from "@/actions/notifications/mark-as-read";

interface NotificationListProps {
  notifications: Notification[];
  onNotificationRead?: () => void;
}

export default function NotificationList({ 
  notifications, 
  onNotificationRead 
}: NotificationListProps) {
  const router = useRouter();
  const [currentNotifications, setCurrentNotifications] = useState(notifications);

  useEffect(() => {
    setCurrentNotifications(notifications);
  }, [notifications]);

  const getNotificationIcon = (type: NotificationType) => {
    switch (type) {
      case "CONTRACT_EXPIRING":
      case "CONTRACT
```

## `NotificationSettings.tsx`

```ts
// Path: components/notifications/NotificationSettings.tsx
"use client";

import { useState, useEffect } from "react";
import { Checkbox } from "@/components/ui/checkbox";
import { Label } from "@/components/ui/label";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Separator } from "@/components/ui/separator";
// Uvezite vaš custom useToast hook
//import { useToast } from "sonner";
import { NotificationPreferences } from "@/lib/types/notification-types";
import { updateNotificationPreferences } from "@/actions/notifications/update-preferences";
import { useTransition } from "react";

interface NotificationSettingsProps {
    initialPreferences: NotificationPreferences;
}

type PreferencesState = NotificationPreferences;

export default function NotificationSettings({ initialPreferences }: NotificationSettingsProps) {
    const [preferences, setPreferences] = useState<PreferencesState>(initialPre
```

## `OperatorCard.tsx`

```ts
// components/operators/OperatorCard.tsx


"use client";

import { Operator } from "@prisma/client";
import { Badge } from "@/components/ui/badge";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardFooter } from "@/components/ui/card";
import Link from "next/link";
import { Globe, Mail, Phone } from "lucide-react";
import Image from "next/image";

interface OperatorCardProps {
  operator: Operator;
}

export function OperatorCard({ operator }: OperatorCardProps) {
  return (
    <Card className="flex h-full flex-col">
      <CardContent className="flex flex-1 flex-col pt-6">
        <div className="mb-4 flex items-center justify-between">
          {operator.logoUrl ? (
            <div className="relative h-12 w-12 overflow-hidden rounded-md">
              <Image
                src={operator.logoUrl}
                alt={`${operator.name} logo`}
                fill
                className="object-contain"
              />
            </div>
        
```

## `OperatorContracts.tsx`

```ts
// components/operators/OperatorContracts.tsx


"use client";

import { useEffect, useState } from "react";
import { Contract } from "@prisma/client";
import { useRouter } from "next/navigation";
import { CalendarIcon, ExternalLink } from "lucide-react";
import { format } from "date-fns";

import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Skeleton } from "@/components/ui/skeleton";
import { Badge } from "@/components/ui/badge";
import { getContractsByOperatorId } from "@/actions/operators";
import Link from "next/link";
import { formatCurrency } from "@/lib/formatters";

interface OperatorContractsProps {
  operatorId: string;
}

export function OperatorContracts({ operatorId }: OperatorContractsProps) {
  const [contracts, setContracts] = useState<Contract[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const router = useRouter();

  useEffect(() => {
    
```

## `OperatorDetails.tsx`

```ts
// components/operators/OperatorDetails.tsx


"use client";

import { Operator } from "@prisma/client";
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import { formatDate } from "@/lib/utils";
import { Globe, Mail, Phone, Calendar, FileEdit } from "lucide-react";
import Image from "next/image";

interface OperatorDetailsProps {
  operator: Operator;
}

export function OperatorDetails({ operator }: OperatorDetailsProps) {
  return (
    <div className="grid gap-6">
      <Card>
        <CardHeader>
          <CardTitle className="flex items-center justify-between">
            <span>Operator Information</span>
            <Badge variant={operator.active ? "success" : "destructive"}>
              {operator.active ? "Active" : "Inactive"}
            </Badge>
          </CardTitle>
          <CardDescription>
            Basic details about the operator
          </CardDescription
```

## `OperatorFilters.tsx`

```ts
// components/operators/OperatorFilters.tsx


"use client";

import { usePathname, useRouter, useSearchParams } from "next/navigation";
import { Button } from "@/components/ui/button";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuLabel,
  DropdownMenuRadioGroup,
  DropdownMenuRadioItem,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";
import { Filter } from "lucide-react";

export function OperatorFilters() {
  const router = useRouter();
  const pathname = usePathname();
  const searchParams = useSearchParams();
  
  // Get current filters from URL
  const currentStatus = searchParams?.get("status") || "all";

  // Update the URL with new filters
  const createQueryString = (name: string, value: string) => {
    const params = new URLSearchParams(searchParams?.toString());
    
    if (value === "all") {
      params.delete(name);
    } else {
      params.set(name, value);
    }
    
    return params.toString();
  };

  // Hand
```

## `OperatorForm.tsx`

```ts
// components/operators/OperatorForm.tsx

"use client";

import { zodResolver } from "@hookform/resolvers/zod";
import { useForm } from "react-hook-form";
import { useState } from "react";
import { useRouter } from "next/navigation";
import { toast } from "sonner";

import { Button } from "@/components/ui/button";
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Checkbox } from "@/components/ui/checkbox";
import { Card, CardContent } from "@/components/ui/card";
import { Loader2 } from "lucide-react";

import { operatorSchema, type OperatorFormValues } from "@/schemas/operator";
import { createOperator, updateOperator } from "@/actions/operators";
import { Operator } from "@prisma/client";

interface OperatorFormProps {
  operator?: Operator;
}

export function OperatorForm({ operator }: Operat
```

## `OperatorList.tsx`

```ts
// components/operators/OperatorList.tsx

"use client";

import { useMemo, useState } from "react";
import {
  ColumnDef,
  flexRender,
  getCoreRowModel,
  getPaginationRowModel,
  getSortedRowModel,
  SortingState,
  useReactTable,
} from "@tanstack/react-table";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Badge } from "@/components/ui/badge";
import { Operator } from "@prisma/client";
import Link from "next/link";
import { useSearchParams } from "next/navigation";
import { OperatorFilters } from "./OperatorFilters";
import { PlusCircle } from "lucide-react";
import { DataTablePagination } from "@/components/ui/data-table-pagination";
import { formatDate } from "@/lib/utils";

interface DataTableProps {
  data: Operator[] | any; // Allow any type temporarily for robustness
  canCreate?: boolean;
}

export func
```

## `ParkingServiceCard.tsx`

```ts
//components/parking-services/ParkingServiceCard.tsx

"use client";

import Link from "next/link";
import { MapPin, Phone, Mail, Calendar } from "lucide-react";
import { 
  Card, 
  CardHeader, 
  CardTitle, 
  CardContent, 
  CardFooter 
} from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import { Button } from "@/components/ui/button";
import { ParkingServiceItem } from "@/lib/types/parking-service-types";
import { formatDate } from "@/lib/utils";

interface ParkingServiceCardProps {
  service: ParkingServiceItem;
}

export default function ParkingServiceCard({ service }: ParkingServiceCardProps) {
  return (
    <Card className="h-full flex flex-col">
      <CardHeader className="pb-2">
        <div className="flex justify-between items-start">
          <CardTitle className="text-lg font-medium">{service.name}</CardTitle>
          <Badge variant={service.isActive ? "default" : "secondary"}>
            {service.isActive ? "Active" : "Inactive"}
          
```

## `ParkingServiceContracts.tsx`

```ts
//components/parking-services/ParkingServiceContracts.tsx

"use client";

import React, { useState, useEffect } from "react";
import { useParkingServiceContracts } from "@/hooks/use-parking-service-contracts";
import { Button } from "@/components/ui/button";
import { Card, CardContent } from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { format } from "date-fns";
import { Loader2, RefreshCw } from "lucide-react";
import Link from "next/link";
import EmptyState from "@/components/EmptyState";
import { ContractStatus } from "@prisma/client"; // Assuming ContractStatus enum is available

// Define the expected structure of a contract item, including the operator relation
interface ContractItem {
  id: string;
  contractNumber: string;
  name: string;
  startDate: Date;
  endDate: Date;
  status: ContractStatus; // Use the enum type
  revenueP
```

## `ParkingServiceDetails.tsx`

```ts
//components/parking-services/ParkingServiceDetails.tsx
"use client";

import { useState } from "react";
import Link from "next/link";
import { useRouter } from "next/navigation";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import { Button } from "@/components/ui/button";
import {
  Phone,
  Mail,
  MapPin,
  Calendar,
  Clock,
  FileEdit,
  Trash,
  AlertCircle,
  Copy,
  Banknote
} from "lucide-react";
import { ParkingServiceDetail } from "@/lib/types/parking-service-types";
import { formatDate, formatCurrency } from "@/lib/utils";
import {
  AlertDialog,
  AlertDialogAction,
  AlertDialogCancel,
  AlertDialogContent,
  AlertDialogDescription,
  AlertDialogFooter,
  AlertDialogHeader,
  AlertDialogTitle,
  AlertDialogTrigger,
} from "@/components/ui/alert-dialog";
import { deleteService } from "@/actions/parking-services/delete";
import { toast } from "sonner";

interface ParkingServiceDetailsProps {

```

## `ParkingServiceFilters.tsx`

```ts
//components/parking-services/ParkingServiceFilters.tsx
"use client";

import { useState, useEffect } from "react";
import { useRouter, useSearchParams } from "next/navigation";
import { zodResolver } from "@hookform/resolvers/zod";
import { useForm } from "react-hook-form";
import { z } from "zod";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
} from "@/components/ui/form";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { Search, X } from "lucide-react";
import { Card, CardContent } from "@/components/ui/card";

const filterSchema = z.object({
  searchTerm: z.string().optional(),
  isActive: z.enum(["all", "true", "false"]).default("all"),
});

type FilterFormValues = z.infer<typeof filterSchema>;

interface ParkingServiceFiltersProps {
  initialFilters?: {
    searchTerm?: string;
    isActiv
```

## `ParkingServiceForm.tsx`

```ts
//components/parking-services/ParkingServiceForm.tsx
//components/parking-services/ParkingServiceForm.tsx

"use client";

import { useState } from "react";
import { useRouter } from "next/navigation";
import { useForm, useFieldArray } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { createParkingServiceSchema } from "@/schemas/parking-service";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Button } from "@/components/ui/button";
import { Switch } from "@/components/ui/switch";
import { Plus, X } from "lucide-react";
import { toast } from "sonner";
import { ParkingServiceFormData } from "@/lib/types/parking-service-types";
import { create } from "@/actions/parking-services/create";
import { update } from "@/actions/parking-services/update";

interface ParkingServiceFormProp
```

## `ParkingServiceList.tsx`

```ts
//components/parking-services/ParkingServiceList.tsx
"use client";

import { useState, useEffect } from "react";
import { useRouter, useSearchParams } from "next/navigation";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { Button } from "@/components/ui/button";
import { Pencil, Trash, Eye, ChevronLeft, ChevronRight, ArrowUpDown, ArrowUp, ArrowDown } from "lucide-react";
import { Badge } from "@/components/ui/badge";
import { ParkingServiceItem } from "@/lib/types/parking-service-types";
import { formatDate } from "@/lib/utils";
import { AlertDialog, AlertDialogAction, AlertDialogCancel, AlertDialogContent, AlertDialogDescription, AlertDialogFooter, AlertDialogHeader, AlertDialogTitle, AlertDialogTrigger } from "@/components/ui/alert-dialog";
import { deleteService } from "@/actions/parking-services/delete";
import { toast } from "sonner";

interface ParkingServiceListProps {
  parkingServices: ParkingServiceI
```

## `ParkingServiceReports.tsx`

```ts
// components/parking-services/ParkingServiceReports.tsx
"use client";

import React, { useEffect, useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";

interface Report {
  id: string;
  title: string;
  createdAt: string;
  status: string;
}

interface Props {
  parkingServiceId: string;
}

export default function ParkingServiceReports({ parkingServiceId }: Props) {
  const [reports, setReports] = useState<Report[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    async function fetchReports() {
      setLoading(true);
      setError(null);
      try {
        const res = await fetch(`/api/parking-services/${parkingServiceId}/reports`);
        if (!res.ok) throw new Error("Failed to fetch reports");
        const data = await res.json();
        setReports(data.reports || []);
      } catch (err: any) {
        setErro
```

## `ParkingServiceServicesOverview.tsx`

```ts
//components/parking-services/ParkingServiceServicesOverview.tsx

"use client";

import React, { useState, useEffect, useCallback } from "react";
import Link from "next/link";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table";
import { Loader2, PlusCircle, Link as LinkIcon, Eye, EyeOff } from "lucide-react";
import EmptyState from "@/components/EmptyState";
import { ServiceType } from "@prisma/client";
import ServiceLinkToContractModal from "@/components/contracts/ServiceLinkToContractModal";

import { getServicesForParkingServiceContracts } from "@/actions/parking-services/getServicesForParkingServiceContracts";

interface ServiceForContract {
  id: string;
  name: string;
  type: ServiceType;
  description: string | null;
  contractCount: number;
}

interface ParkingServiceServicesOverviewProps {
  parkin
```

## `ProductCard.tsx`

```ts
// /components/products/ProductCard.tsx
'use client';

import Link from 'next/link';
// Uvozimo UI komponente iz Shadcn UI
import { Card, CardHeader, CardTitle, CardDescription, CardContent } from "@/components/ui/card"; // Removed CardFooter
import { Badge } from "@/components/ui/badge";
// Uvozimo AŽURIRANI tip za Proizvod
import { ProductWithDetails } from '@/lib/types/product-types';


interface ProductCardProps {
    // Prima AŽURIRANI tip Proizvoda
    product: ProductWithDetails;
}

/**
 * Komponenta za prikaz kartice sa sažetkom informacija o proizvodu.
 * Usklađena sa Product modelom u schema.prisma i ažuriranim tipovima.
 * @param product - Objekat proizvoda koji treba prikazati.
 */
export function ProductCard({ product }: ProductCardProps) {

    return (
        // Omotajte karticu Linkom ka stranici sa detaljima proizvoda
        <Link href={`/products/${product.id}`} passHref>
            <Card className="h-full flex flex-col hover:shadow-lg transition-shadow cursor-poin
```

## `ProductFilters.tsx`

```ts
// components/products/ProductFilters.tsx
'use client';

import { useState, useCallback, useEffect } from "react";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Label } from "@/components/ui/label";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Search, X } from "lucide-react";
import { ProductFilterOptions } from "@/lib/types/product-types";

interface ProductFiltersProps {
  onFilterChange: (filters: ProductFilterOptions) => void;
  initialFilters?: ProductFilterOptions;
}

/**
 * Component for filtering products by search term and active status
 */
export function ProductFilters({ onFilterChange, initialFilters }: ProductFiltersProps) {
  // Local state for filters
  const [search, setSearch] = useState(initialFilters?.search || "");
  const [isActive, setIsActive] = useState<bool
```

## `ProductForm.tsx`

```ts
// /components/products/ProductForm.tsx
'use client';

import { useEffect, useState } from 'react';
import { useRouter } from 'next/navigation';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

// Uvozimo AŽURIRANU Zod šemu i tip za podatke proizvoda
import { productSchema, ProductFormData } from '@/schemas/product';
// Uvozimo AŽURIRANE Server Akcije za kreiranje i ažuriranje proizvoda
import { createProduct } from '@/actions/products/create';
import { updateProduct } from '@/actions/products/update';
// Uvozimo tip za Proizvod sa detaljima (ako je potreban za initialData)
import { ProductWithDetails } from '@/lib/types/product-types';

// Uvozimo UI komponente iz Shadcn UI
import { Button } from '@/components/ui/button';
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from '@/components/ui/form';
import { Input } from '@/components/ui/input';
import { Textarea } from '@/c
```

## `ProductList.tsx`

```ts
// /components/products/ProductList.tsx
'use client';

import { useState, useCallback } from "react"; // useState za delete modal
import Link from "next/link";
import { useRouter } from "next/navigation";
// Uvozimo AŽURIRANI hook za dohvatanje proizvoda
import { useProducts } from "@/hooks/use-products"; // useProducts koristi getProducts akciju
// Uvozimo AŽURIRANI tip za Proizvod
import { ProductWithDetails, ProductFilterOptions } from "@/lib/types/product-types";
// Uvozimo komponentu filtera (AŽURIRANU)
import { ProductFilters } from "@/components/products/ProductFilters"; // Komponenta koju smo generisali/ažurirali
// Uvozimo AŽURIRANU server akciju za brisanje proizvoda
import { deleteProduct } from "@/actions/products/delete";
// Uvozimo UI komponente iz Shadcn UI
import { Button } from "@/components/ui/button";
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table";
import { Pagination, PaginationContent, PaginationItem, Paginatio
```

## `ProviderCard.tsx`

```ts
// components/providers/ProviderCard.tsx
"use client";

import React, { useState, useEffect } from "react"; // Import useEffect for logging
import Link from "next/link";
import { useRouter } from "next/navigation";
import {
  Card,
  CardContent,
  CardFooter,
  CardHeader,
  CardTitle
} from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Badge } from "@/components/ui/badge";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";
import { MoreVertical, Check, X, RefreshCw, FileText, CircleCheck, CircleX, Loader2 } from "lucide-react";
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogDescription,
  DialogFooter,
} from "@/components/ui/dialog";
import { Label } from "@/components/ui/label";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Checkbox } from "@/components/
```

## `ProviderContracts.tsx`

```ts
//components/providers/ProviderContracts.tsx
"use client";

import React, { useState } from "react";
import { useProviderContracts } from "@/hooks/use-provider-contracts";
import { Button } from "@/components/ui/button";
import { Card, CardContent } from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { format } from "date-fns";
import { Loader2, RefreshCw } from "lucide-react";
import Link from "next/link";
import EmptyState from "@/components/EmptyState";
import { ContractStatus } from "@prisma/client";

interface ProviderContractItem {
  id: string;
  contractNumber: string;
  name: string;
  startDate: Date;
  endDate: Date;
  status: ContractStatus;
  revenuePercentage: number;
  isRevenueSharing: boolean;
  operatorRevenue?: number | null;
  operator?: {
     id: string;
     name: string;
  } | null;
}

interface ProviderContractsProps
```

## `ProviderDetails.tsx`

```ts
// /components/providers/ProviderDetails.tsx
'use client';

import { ProviderWithDetails } from '@/lib/types/provider-types';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { Separator } from '@/components/ui/separator'; // Keep Separator if you want it
import { formatDate } from "@/lib/utils"; // Assuming formatDate exists
import { AlertCircle, Phone, Mail, MapPin, Calendar, Clock } from "lucide-react"; // Import icons
import React from 'react'; // Import React

interface ProviderDetailsProps {
    provider: ProviderWithDetails;
}

const ProviderDetails: React.FC<ProviderDetailsProps> = ({ provider }) => {
    // Pretpostavljamo da provider prop nikada neće biti null/undefined
    // Parent page (npr. [id]/page.tsx) treba da rukuje 404 stanjem ako provajder nije pronađen.

    return (
        <div className="space-y-6">
            {/* Contact Information Card - Styled like ParkingServiceDetails */}
            <Card>
                <CardHead
```

## `ProviderFilters.tsx`

```ts
// components/providers/ProviderFilters.tsx
"use client";

import { useState, useEffect } from "react";
import { Input } from "@/components/ui/input";
import { 
  Select, 
  SelectContent, 
  SelectItem, 
  SelectTrigger, 
  SelectValue 
} from "@/components/ui/select";
import { Checkbox } from "@/components/ui/checkbox";
import { Button } from "@/components/ui/button";
import { Search, X } from "lucide-react";

interface FilterOptions {
  search?: string;
  isActive?: boolean;
  hasContracts?: boolean;
  hasComplaints?: boolean;
  sortBy?: string;
  sortDirection?: "asc" | "desc";
}

interface ProviderFiltersProps {
  initialFilters: FilterOptions;
  onFilterChange: (filters: FilterOptions) => void;
  loading?: boolean;
}

export function ProviderFilters({
  initialFilters,
  onFilterChange,
  loading = false
}: ProviderFiltersProps) {
  const [filters, setFilters] = useState<FilterOptions>({
  ...initialFilters,
  hasContracts: initialFilters.hasContracts || false,
  hasComplaints: i
```

## `ProviderForm.tsx`

```ts
// /components/providers/ProviderForm.tsx
"use client";

import { useState, useEffect } from "react";
import { useRouter } from "next/navigation";
import { useForm, Controller } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";

import { createProvider } from "@/actions/providers/create";
import { updateProvider } from "@/actions/providers/update";

import { providerSchema, ProviderFormData } from "@/schemas/provider";


import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Checkbox } from "@/components/ui/checkbox";
import { Label } from "@/components/ui/label";
import { Textarea } from "@/components/ui/textarea";
import {
    Form,
    FormControl,
    FormField,
    FormItem,
    FormLabel,
    FormMessage,
} from "@/components/ui/form";
import { Card, CardHeader, CardTitle, CardContent, CardFooter } from "@/components/ui/card";
import { toast } from "sonner";


interface ProviderFormProps {
    provider
```

## `ProviderList.tsx`

```ts
//components/providers/ProviderList.tsx
"use client";

import { useState, useCallback, useEffect } from "react";
import { Pagination, PaginationContent, PaginationItem, PaginationLink, PaginationNext, PaginationPrevious } from "@/components/ui/pagination";
import { ProviderCard } from "@/components/providers/ProviderCard";
import { ProviderFilters } from "@/components/providers/ProviderFilters";
import { useProviders } from "@/hooks/use-providers";
import { toast } from "sonner";
import ProviderLogList from "@/components/providers/ProviderLogList";
import { useDebounce } from '@/hooks/useDebounce';

export function ProviderList() {
  const {
    providers,
    total,
    pagination,
    loading,
    error,
    setPagination,
    filters,
    setFilters,
    refreshData
  } = useProviders();

  const [actionLoading, setActionLoading] = useState(false);
  const [logRefreshKey, setLogRefreshKey] = useState(0);
  
  // Debounce filter changes
  
  
  // Sync debounced filters with hook


 
```

## `ProviderLogList.tsx`

```ts
//components/providers/ProviderLogList.tsx

"use client";

import React, { useState } from 'react';
import { Pagination, PaginationContent, PaginationItem, PaginationLink, PaginationNext, PaginationPrevious } from "@/components/ui/pagination";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import { Button } from "@/components/ui/button";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { format } from "date-fns";
import { Loader2, CheckCircle2, ArrowUpDown, ArrowUp, ArrowDown } from "lucide-react";
import EmptyState from "@/components/EmptyState";
import { LogActionType, LogStatus } from "@prisma/client";

import { useProviderLogs } from "@/hooks/use-provider-logs";
import { LogFilters } from "@/components/logs/LogFilters";
import Link from "next/link";
import { toast } from "sonner";

import { updateLogStatus } from "@/actions/log/u
```

## `ProviderServicesForContracts.tsx`

```ts

//components/providers/ProviderServicesForContracts.tsx

"use client";

import React, { useState, useEffect, useCallback } from "react";
import Link from "next/link";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table";
import { Loader2, FileText, PlusCircle } from "lucide-react";
import EmptyState from "@/components/EmptyState";
import { ServiceType } from "@prisma/client"; // Uvezite ServiceType

// Uvezite novu server akciju
import { getServicesForNewContracts } from "@/actions/providers/getServicesForNewContracts";

interface ServiceForContract {
  id: string;
  name: string;
  type: ServiceType;
  description: string | null;
}

interface ProviderServicesForContractsProps {
  providerId: string;
  providerName: string; // Dodajte providerName za pre-popunjavanje forme za ugovor
}

export default function
```

## `ProviderServicesOverview.tsx`

```ts
//components/providers/ProviderServicesOverview.tsx
"use client";

import React, { useState, useEffect, useCallback } from "react";
import Link from "next/link";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table";
import { Loader2, FileText, PlusCircle, Link as LinkIcon, Eye, EyeOff } from "lucide-react"; // Dodat Eye, EyeOff
import EmptyState from "@/components/EmptyState";
import { ServiceType } from "@prisma/client";

import { getServicesForNewContracts } from "@/actions/providers/getServicesForNewContracts";
import ServiceLinkToContractModal from "@/components/contracts/ServiceLinkToContractModal";

interface ServiceForContract {
  id: string;
  name: string;
  type: ServiceType;
  description: string | null;
}

interface ProviderServicesOverviewProps {
  providerId: string;
  providerName: string;
}

ex
```

## `ExcelGenerator.tsx`

```ts
//components/reports/ExcelGenerator.tsx


"use client";

import { useState } from "react";
import { Calendar, FileSpreadsheet, Filter, Settings, Loader2 } from "lucide-react"; // Added Loader2 for the button spinner
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Checkbox } from "@/components/ui/checkbox";
import { Label } from "@/components/ui/label";
// Assuming DatePicker is a component you have elsewhere, potentially using react-day-picker inside
import { DatePicker } from "@/components/ui/date-picker";
import { toast } from "sonner"; // Assuming sonner for toasts
import { ScrollArea } from "@/components/ui/scroll-area";
import { cn } from "@/lib/utils"; // Added cn for potential cl
```

## `ExportOptions.tsx`

```ts
///components/reports/ExportOptions.tsx

"use client";

import { useState } from "react";
import { Check, Download, FileSpreadsheet, FileText, Share2 } from "lucide-react";
import { Button } from "@/components/ui/button";
import { DropdownMenu, DropdownMenuContent, DropdownMenuItem, DropdownMenuTrigger } from "@/components/ui/dropdown-menu";
import { toast } from "sonner";

export type ExportFormat = "excel" | "pdf" | "csv";

interface ExportOptionsProps {
  onExport: (format: ExportFormat) => Promise<void>;
  disabled?: boolean;
  allowedFormats?: ExportFormat[];
}

export function ExportOptions({
  onExport,
  disabled = false,
  allowedFormats = ["excel", "csv", "pdf"],
}: ExportOptionsProps) {
  const [isExporting, setIsExporting] = useState(false);
  
  const handleExport = async (format: ExportFormat) => {
    if (isExporting) return;
    
    setIsExporting(true);
    toast.loading(`Preparing ${format.toUpperCase()} export...`);
    
    try {
      await onExport(format);
     
```

## `ReportPreview.tsx`

```ts
// components/reports/ReportPreview.tsx
"use client";

import { useState } from "react";
import { ChevronLeft, ChevronRight, Download, Printer } from "lucide-react";
import { Button } from "@/components/ui/button";
import { Card } from "@/components/ui/card";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Skeleton } from "@/components/ui/skeleton";
import { cn } from "@/lib/utils";

interface ReportPreviewProps {
  reportId?: string;
  reportType: string;
  reportName: string;
  reportData?: any;
  isLoading?: boolean;
  onDownload?: () => void;
  onPrint?: () => void;
  previewImageUrl?: string; // Optional URL to preview image
  previewHtml?: string; // Optional HTML content for preview
}

export function ReportPreview({
  reportId,
  reportType,
  reportName,
  reportData,
  isLoading = false,
  onDownload,
  onPrint,
  previewImageUrl,
  previewHtml,
}: ReportPreviewProps) {
  const [currentPage, setCurrentPage] = useState(1);
  const [tot
```

## `ScheduleForm.tsx`

```ts
///components/reports/ScheduleForm.tsx

"use client";

import { useState } from "react";
import { Calendar } from "lucide-react";
import { zodResolver } from "@hookform/resolvers/zod";
import { useForm } from "react-hook-form";
import { z } from "zod";
import { Button } from "@/components/ui/button";
import { Form, FormControl, FormDescription, FormField, FormItem, FormLabel, FormMessage } from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Textarea } from "@/components/ui/textarea";
import { RadioGroup, RadioGroupItem } from "@/components/ui/radio-group";
import { toast } from "sonner";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";

const reportFrequencies = [
  { value: "DAILY", label: "Daily" },
  { value: "WEEKLY", label: "Weekly" },
  { value: "MONTHLY", label: "Monthly" },
  { value: "QUARTERLY
```

## `ActivityLog.tsx`

```ts
// Final ActivityLog.tsx code (with optional initial props, adjusted useEffect, debouncing, split Entity columns, NO console logs)

// Path: components/security/ActivityLog.tsx

"use client";

import React, { useState, useEffect, useRef } from "react";
import { LogSeverity } from "@prisma/client";
import { format } from "date-fns";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow
} from "@/components/ui/table";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Calendar } from "@/components/ui/calendar";
import { Popover, PopoverContent, PopoverTrigger } from "@/components/ui/popover";
import { CalendarIcon, FilterIcon, RefreshCw } from "lucide-react";
import { Badge } from "@/components/ui/badge";
import {
  Pagination,
  PaginationContent,
  PaginationItem,
  PaginationPrevious,
  Pag
```

## `ActivityLogTable.tsx`

```ts
// Path: components/security/ActivityLogTable.tsx

import React from 'react'; // Nije "use client" jer je ovo samo renderovanje
import { LogSeverity } from "@prisma/client";
import { format } from "date-fns";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow
} from "@/components/ui/table";
import { Badge } from "@/components/ui/badge";

interface ActivityLogEntry {
  id: string;
  action: string;
  entityType: string | null;
  entityId?: string | null;
  details?: string | null;
  severity: LogSeverity;
  userId: string | null;
  user?: {
    id: string;
    name?: string | null;
    email: string;
    role: string;
  } | null;
  createdAt: Date;
}

interface ActivityLogTableProps {
  logs: ActivityLogEntry[]; // Prihvata logove kao prop
}

export default function ActivityLogTable({ logs }: ActivityLogTableProps) {

  const severityColors = {
    [LogSeverity.INFO]: "bg-blue-100 text-blue-800",
    [LogSeverity.WARNING]: "bg-yellow-100 text-yellow-800",
```

## `APIResponseTimes.tsx`

```ts
// Path: components/security/APIResponseTimes.tsx

"use client";

import React from 'react';
import { Card, CardContent } from "@/components/ui/card";

// Ova komponenta ce prikazivati grafikone ili tabele za API Response Times
// Trebace joj useEffect za dohvatanje podataka sa /api/security/performance/api?timeRange=...
// i state za cuvanje tih podataka.

export default function APIResponseTimes() {
   // Implementirajte logiku dohvatanja podataka (useEffect + fetch)
   // Implementirajte prikaz (grafikoni/tabele za vreme odgovora po endpointu)

  return (
    <CardContent className="flex items-center justify-center h-64">
      <p className="text-muted-foreground">API Response Times Component (Placeholder)</p>
      {/* Ovde ce biti grafikoni/tabele za API vremena odgovora */}
    </CardContent>
  );
}
```

## `BlackLogTable.tsx`

```ts
// Path: components/security/BlackLogTable.tsx

"use client";

import { BlacklistLog, LogBlackType } from "@prisma/client";
import { format } from "date-fns";
import { Badge } from "@/components/ui/badge";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "@/components/ui/card";
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table";
import { Collapsible, CollapsibleContent, CollapsibleTrigger } from "@/components/ui/collapsible";
import { ChevronDown, ChevronRight, User, Calendar, FileText } from "lucide-react";
import { useState, Fragment } from "react";

interface BlacklistLogWithUser extends BlacklistLog {
  user: {
    id: string;
    name: string | null;
  };
}

interface AuditLogTableProps {
  logs: BlacklistLogWithUser[];
}

interface ParsedData {
  senderName?: string;
  isActive?: boolean;
  description?: string;
  effectiveDate?: string;
  matchCount?: number;
  lastMatchDate?: string;
  createdBy?: { nam
```

## `DatabasePerformance.tsx`

```ts
// Path: components/security/DatabasePerformance.tsx

"use client";

import React from 'react';
import { Card, CardContent } from "@/components/ui/card";

// Ova komponenta ce prikazivati grafikone ili tabele za Database Performance
// Trebace joj useEffect za dohvatanje podataka sa /api/security/performance/database?timeRange=...
// i state za cuvanje tih podataka.

export default function DatabasePerformance() {
  // Implementirajte logiku dohvatanja podataka (useEffect + fetch)
  // Implementirajte prikaz (grafikoni/tabele za vreme izvrsavanja upita, itd.)

  return (
     <CardContent className="flex items-center justify-center h-64">
      <p className="text-muted-foreground">Database Performance Component (Placeholder)</p>
      {/* Ovde ce biti grafikoni/tabele za performanse baze */}
    </CardContent>
  );
}
```

## `PerformanceMetrics.tsx`

```ts
// components/security/PerformanceMetrics.tsx
"use client";

import React, { useState } from 'react';
import { usePerformanceMetrics, TimeRange } from "@/hooks/use-performance-metrics"; // Import hook and TimeRange type
import { Card, CardContent, CardHeader, CardTitle, CardDescription } from "@/components/ui/card";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Label } from "@/components/ui/label";
import { Loader2, RefreshCw } from "lucide-react";
import { Button } from "@/components/ui/button";
import { format } from 'date-fns'; // Za formatiranje datuma/vremena
// Importovane ikone koje se koriste u sumarnim karticama
import { AlertTriangle, Cpu, Activity, Clock, HardDrive } from "lucide-react";


// Helper funkcija za formatiranje vrednosti (možete je premestiti u lib/utils ako je koristite na više mesta)
const formatValue = (value: number | null, type: string) => {
    if (value === null) return "--";
    switch(ty
```

## `PermissionGate.tsx`

```ts
///components/security/PermissionGate.tsx

"use client";

import React from "react";
import { useSession } from "next-auth/react";
import { UserRole } from "@prisma/client";

interface PermissionGateProps {
  children: React.ReactNode;
  allowedRoles?: UserRole[];
  fallback?: React.ReactNode;
}

/**
 * A component that conditionally renders children based on user role permissions
 * 
 * @param children - Content to show if user has permission
 * @param allowedRoles - Array of roles that are allowed to view the content
 * @param fallback - Optional content to show if user doesn't have permission
 */
const PermissionGate: React.FC<PermissionGateProps> = ({
  children,
  allowedRoles = [],
  fallback = null,
}) => {
  const { data: session, status } = useSession();
  const userRole = session?.user?.role as UserRole | undefined;
  
  // Loading state
  if (status === "loading") {
    return <div className="animate-pulse p-4">Loading permissions...</div>;
  }
  
  // Not authenticated
  if
```

## `RolePermissions.tsx`

```ts
// Path: components/security/RolePermissions.tsx

"use client";

import React from 'react';
import { CardContent } from "@/components/ui/card";

// Ova komponenta ce prikazivati i omogucavati izmenu permisija po ulogama
// Trebace joj useEffect za dohvatanje uloga i permisija,
// state za podatke, i UI za prikaz i izmenu.

export default function RolePermissions() {
  // Implementirajte logiku dohvatanja uloga i njihovih permisija
  // Implementirajte UI za prikaz i izmenu permisija po ulogama

  return (
    <CardContent className="flex items-center justify-center h-64">
      <p className="text-muted-foreground">Role Permissions Component (Placeholder)</p>
      {/* Ovde ce biti UI za upravljanje permisijama uloga */}
    </CardContent>
  );
}
```

## `SystemResources.tsx`

```ts
// Path: components/security/SystemResources.tsx

"use client";

import React from 'react';
import { Card, CardContent } from "@/components/ui/card";

// Ova komponenta ce prikazivati grafikone ili tabele za System Resources (CPU, Memory, Disk)
// Trebace joj useEffect za dohvatanje podataka sa /api/security/performance/resources?timeRange=...
// i state za cuvanje tih podataka.

export default function SystemResources() {
  // Implementirajte logiku dohvatanja podataka (npr. useEffect + fetch)
  // Implementirajte prikaz (npr. grafikoni koriscenjem Recharts)

  return (
    <CardContent className="flex items-center justify-center h-64">
      <p className="text-muted-foreground">System Resources Component (Placeholder)</p>
      {/* Ovde ce biti grafikoni i tabela za CPU, Memory, Disk */}
    </CardContent>
  );
}
```

## `UserRoleManagement.tsx`

```ts
// Path: components/security/UserRoleManagement.tsx

"use client";

import React from 'react';
import { CardContent } from "@/components/ui/card";

// Ova komponenta ce upravljati listom korisnika i dodeljivanjem uloga
// Trebace joj useEffect za dohvatanje korisnika i uloga,
// state za podatke, i UI za prikaz i izmenu.

export default function UserRoleManagement() {
  // Implementirajte logiku dohvatanja korisnika i uloga
  // Implementirajte UI za tabelu korisnika, dodelu uloga, itd.

  return (
    <CardContent className="flex items-center justify-center h-64">
      <p className="text-muted-foreground">User Role Management Component (Placeholder)</p>
      {/* Ovde ce biti UI za upravljanje ulogama korisnika */}
    </CardContent>
  );
}
```

## `ImportForm.tsx`

```ts
// Path: components/services/ImportForm.tsx
'use client';

import { useState } from 'react';
import { useForm } from 'react-hook-form';
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

// Uvoz Server Akcije i tipa rezultata (ImportResult tip se koristi ovde)
import { importVasServiceData, type VasImportResult } from '@/actions/services/importVasData';

// Uvozimo Button
import { Button } from '@/components/ui/button';

// Ostavljamo Input i Form komponente
import { Input } from '@/components/ui/input';
import { Form, FormControl, FormField, FormItem, FormLabel, FormMessage } from '@/components/ui/form';

// Uvozimo custom useToast hook (koristite VAŠU STVARNU putanju)
// import { useToast } from "@/components/toast/toast-context"; // <-- Proverite/Ispravite putanju!

// Uvozimo Card komponente
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';

// Ostavljamo komponente za prikaz rezultata
import { Table, TableBody, TableCell
```

## `ParkingServiceProcessorForm.tsx`

```ts
// Path: components/services/ParkingServiceProcessorForm.tsx


"use client";
import { useState, useRef } from "react";
import toast from "react-hot-toast";
import { useSession } from "next-auth/react";

interface LogEntry {
  file: string;
  message: string;
  type: 'info' | 'error' | 'success';
  timestamp: Date;
}

interface FileProgress {
  name: string;
  status: 'pending' | 'uploading' | 'processing' | 'completed' | 'error';
  progress: number;
  recordsProcessed?: number;
  parkingServiceId?: string;
  error?: string;
}

export function NewParkingProcessorForm() {
  const { data: session } = useSession();
  const [files, setFiles] = useState<File[]>([]);
  const [processing, setProcessing] = useState(false);
  const [logs, setLogs] = useState<LogEntry[]>([]);
  const [fileProgress, setFileProgress] = useState<FileProgress[]>([]);
  const [overallProgress, setOverallProgress] = useState(0);
  const fileInputRef = useRef<HTMLInputElement>(null);

  const handleFileChange = (e: Reac
```

## `ProviderProcessorForm.tsx`

```ts
// Path: components/services/ProviderProcessorForm.tsx
"use client";

import { useState, useRef } from "react";
import toast from "react-hot-toast";
import { useSession } from "next-auth/react";

interface FileUploadInfo {
  originalName: string;
  savedName: string;
  filePath: string;
  size: number;
  type: string;
  uploadedBy: string;
  uploadedAt: string;
  providerId?: string | null;
}

interface FileProcessingStatus {
  file: File;
  status: 'waiting' | 'uploading' | 'processing' | 'completed' | 'error';
  message?: string;
  reportPath?: string;
  logs: string[];
}

export function ProviderProcessorForm() {
  const { data: session } = useSession();
  const [selectedFiles, setSelectedFiles] = useState<File[]>([]);
  const [processingStatus, setProcessingStatus] = useState<FileProcessingStatus[]>([]);
  const [isProcessing, setIsProcessing] = useState(false);
  const [expandedFile, setExpandedFile] = useState<string | null>(null);
  const fileInputRef = useRef<HTMLInputElement>(
```

## `ServiceCard.tsx`

```ts
///components/services/ServiceCard.tsx


import React from 'react';
import { Card, CardContent, CardFooter, CardHeader, CardTitle } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';
import { Button } from '@/components/ui/button';
import Link from 'next/link';
import { ServiceType } from '@prisma/client';
import { Service } from '@/lib/types/service-types';

interface ServiceCardProps {
  service: Service;
}

export function ServiceCard({ service }: ServiceCardProps) {
  const getTypeColor = (type: ServiceType) => {
    switch (type) {
      case 'VAS':
        return 'bg-blue-100 text-blue-800';
      case 'BULK':
        return 'bg-green-100 text-green-800';
      case 'HUMANITARIAN':
        return 'bg-purple-100 text-purple-800';
      case 'PARKING':
        return 'bg-amber-100 text-amber-800';
      default:
        return 'bg-gray-100 text-gray-800';
    }
  };

  return (
    <Card className="overflow-hidden">
      <CardHeader className="pb-2">
     
```

## `ServiceDetails.tsx`

```ts
// components/services/ServiceDetails.tsx
'use client';
import { useState, useEffect } from "react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";
import { Check, X } from "lucide-react";
import { formatDate } from "@/lib/utils";
import { getServiceById } from "@/actions/services/get";

interface ServiceDetailsProps {
  serviceId: string;
}

export function ServiceDetails({ serviceId }: ServiceDetailsProps) {
  const [service, setService] = useState<any>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    async function loadServiceData() {
      try {
        const result = await getServiceById(serviceId);
        if (result.error) {
          setError(result.error);
        } else if (result.data) {
          setService(result.data);
        } else {
          setError("Service not found");
        }
      } catch (err
```

## `ServiceFilters.tsx`

```ts
///components/services/ServiceFilters.tsx
"use client";
import React from 'react';
import { 
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { Badge } from '@/components/ui/badge';
import { Card, CardContent } from '@/components/ui/card';
import { Search, X, Filter } from 'lucide-react';
import { ServiceType } from '@prisma/client';

interface ServiceFiltersProps {
  currentFilters: {
    type?: string;
    query?: string;
    status?: string;
  };
  onFilterChange: (filters: { type?: string; query?: string; status?: string }) => void;
  resultsCount?: number;
  totalCount?: number;
}

export function ServiceFilters({ 
  currentFilters, 
  onFilterChange, 
  resultsCount, 
  totalCount 
}: ServiceFiltersProps) {
  
  const handleTypeChange = (value: string) => {
    const newFilters = { 
      ...currentFilters, 
      type: va
```

## `ServiceForm.tsx`

```ts
'use client';

import { useEffect, useState } from 'react';
import { useRouter } from 'next/navigation';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import { serviceSchema, ServiceFormData } from '@/schemas/service';
import { createService } from '@/actions/services/create';
import { updateService } from '@/actions/services/update';
import { ServiceWithDetails } from '@/lib/types/service-types';
import { ServiceType } from '@prisma/client';
import { Button } from '@/components/ui/button';
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from '@/components/ui/form';
import { Input } from '@/components/ui/input';
import { Textarea } from '@/components/ui/textarea';
import { Checkbox } from '@/components/ui/checkbox';
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from '@/components/ui/select';
import { toast } from "sonner";
import { Card, CardHea
```

## `ServiceList.tsx`

```ts
///components/services/ServiceList.tsx
"use client";
import { useEffect, useState } from "react";
import Link from "next/link";
import { useRouter, useSearchParams } from "next/navigation";
import { 
  ArrowUpDown, 
  MoreHorizontal, 
  Check, 
  X,
  Loader2,
  Eye,
  ChevronLeft,
  ChevronRight
} from "lucide-react";

import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import { Button } from "@/components/ui/button";
import { Badge } from "@/components/ui/badge";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Skeleton } from "@/components/ui/skeleton";

import { useServices } from "@/hooks/us
```

## `ServiceStats.tsx`

```ts
// /components/services/ServiceStats.tsx
'use client';

// Uvozimo hook za dohvatanje statistika
import { useServiceStats } from '@/hooks/use-service-stats'; // Hook generisan u 5.4
// Uvozimo UI komponente iz Shadcn UI za prikaz
import { Card, CardHeader, CardTitle, CardContent } from "@/components/ui/card";
import { Separator } from "@/components/ui/separator";
// Uvozimo tipove ako su potrebni za prikaz (npr. za RevenueSummary)
// import { RevenueSummary } from '@/lib/services/statistics'; // Tipovi definisani u lib/services/statistics.ts

interface ServiceStatsProps {
    // Opcioni propovi za filtriranje statistika (prosleđuju se hooku useServiceStats)
    serviceIds?: string[];
    productIds?: string[];
    // dateRange?: { from: Date; to: Date }; // Primer opcionalnog propa za datumski opseg
    // Opciono: Naslov komponente ako se koristi na različitim mestima
    title?: string;
}

/**
 * Komponenta za prikaz statističkih podataka o servisima.
 * Koristi useServiceStats hook 
```

## `VasServiceProcessorForm.tsx`

```ts
"use client";

import { useState, useRef, useEffect } from "react";
import { useSession } from "next-auth/react";

interface FileUploadInfo {
  originalName: string;
  savedName: string;
  filePath: string;
  size: number;
  type: string;
  uploadedBy: string;
  uploadedAt: string;
  providerId?: string | null;
}

interface FileProcessingStatus {
  file: File;
  status: 'waiting' | 'uploading' | 'processing' | 'completed' | 'error';
  message?: string;
  progress?: number;
  logs: string[];
}

export function VasServiceProcessorForm() {
  const { data: session, status } = useSession();
  const [selectedFiles, setSelectedFiles] = useState<File[]>([]);
  const [processingStatus, setProcessingStatus] = useState<FileProcessingStatus[]>([]);
  const [isProcessing, setIsProcessing] = useState(false);
  const [expandedFile, setExpandedFile] = useState<string | null>(null);
  const [notifications, setNotifications] = useState<{type: 'success' | 'error', message: string}[]>([]);
  const fileInp
```

## `BulkServiceCard.tsx`

```ts
///components/services/category/BulkServiceCard.tsx

import React from 'react';
import { Card, CardContent, CardFooter, CardHeader, CardTitle } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';
import { Button } from '@/components/ui/button';
import Link from 'next/link';
import { BulkServiceWithRelations } from '@/lib/types/service-types';

interface BulkServiceCardProps {
  bulkService: BulkServiceWithRelations;
}

export function BulkServiceCard({ bulkService }: BulkServiceCardProps) {
  return (
    <Card className="overflow-hidden">
      <CardHeader className="pb-2">
        <div className="flex justify-between items-start">
          <CardTitle className="text-lg font-medium">{bulkService.service_name}</CardTitle>
          <Badge className="bg-green-100 text-green-800">BULK</Badge>
        </div>
        <p className="text-gray-500 text-sm">
          {bulkService.agreement_name}
        </p>
      </CardHeader>
      <CardContent className="pb-2">
   
```

## `HumanServiceCard.tsx`

```ts
///components/services/category/HumanServiceCard.tsx

import React from 'react';
import { Card, CardContent, CardFooter, CardHeader, CardTitle } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';
import { Button } from '@/components/ui/button';
import Link from 'next/link';
import { Service } from '@/lib/types/service-types';

interface HumanServiceCardProps {
  service: Service;
  humanitarianOrg?: { id: string; name: string };
}

export function HumanServiceCard({ service, humanitarianOrg }: HumanServiceCardProps) {
  return (
    <Card className="overflow-hidden">
      <CardHeader className="pb-2">
        <div className="flex justify-between items-start">
          <CardTitle className="text-lg font-medium">{service.name}</CardTitle>
          <Badge className="bg-purple-100 text-purple-800">HUMANITARIAN</Badge>
        </div>
      </CardHeader>
      <CardContent className="pb-2">
        {service.description && (
          <p className="text-sm text-gra
```

## `ParkingServiceCard.tsx`

```ts
///components/services/category/ParkingServiceCard.tsx

import React from 'react';
import { Card, CardContent, CardFooter, CardHeader, CardTitle } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';
import { Button } from '@/components/ui/button';
import Link from 'next/link';
import { Service } from '@/lib/types/service-types';

interface ParkingServiceCardProps {
  service: Service;
  parkingProvider?: { id: string; name: string };
}

export function ParkingServiceCard({ service, parkingProvider }: ParkingServiceCardProps) {
  return (
    <Card className="overflow-hidden">
      <CardHeader className="pb-2">
        <div className="flex justify-between items-start">
          <CardTitle className="text-lg font-medium">{service.name}</CardTitle>
          <Badge className="bg-amber-100 text-amber-800">PARKING</Badge>
        </div>
      </CardHeader>
      <CardContent className="pb-2">
        {service.description && (
          <p className="text-sm text-gr
```

## `VasServiceCard.tsx`

```ts
///components/services/category/VasServiceCard.tsx


import React from 'react';
import { Card, CardContent, CardFooter, CardHeader, CardTitle } from '@/components/ui/card';
import { Badge } from '@/components/ui/badge';
import { Button } from '@/components/ui/button';
import Link from 'next/link';
import { formatCurrency } from '@/lib/utils';
import { format } from 'date-fns';
import { VasServiceWithRelations } from '@/lib/types/service-types';

interface VasServiceCardProps {
  vasService: VasServiceWithRelations;
}

export function VasServiceCard({ vasService }: VasServiceCardProps) {
  return (
    <Card className="overflow-hidden">
      <CardHeader className="pb-2">
        <div className="flex justify-between items-start">
          <CardTitle className="text-lg font-medium">{vasService.proizvod}</CardTitle>
          <Badge className="bg-blue-100 text-blue-800">VAS</Badge>
        </div>
        <p className="text-gray-500 text-sm">
          {format(new Date(vasService.mesec_pr
```

## `DetailSkeleton.tsx`

```ts
// src/components/skeletons/DetailSkeleton.tsx
"use client";

import React from 'react';
// Import your Skeleton component using a named import
import { Skeleton } from "@/components/ui/skeleton";

interface DetailSkeletonProps {
  // You can add props here if you want to customize the layout or number of placeholders
}

const DetailSkeleton: React.FC<DetailSkeletonProps> = () => {
  return (
    <div className="space-y-6 p-6 border rounded-md">
      {/* Placeholder for title/header area */}
      <div className="flex justify-between items-center">
        <Skeleton className="h-8 w-64" />
        <div className="flex gap-2">
           <Skeleton className="h-10 w-24" />
           <Skeleton className="h-10 w-20" />
        </div>
      </div>

      {/* Placeholder sections for details */}
      <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
         {/* Contact Info Section Placeholder */}
         <div className="space-y-4">
             <Skeleton className="h-6 w-48" /> {
```

## `ListSkeleton.tsx`

```ts
// src/components/skeletons/ListSkeleton.tsx
"use client";

import React from 'react';
// Import your Skeleton component using a named import
import { Skeleton } from "@/components/ui/skeleton";

interface ListSkeletonProps {
  count?: number;
  itemHeight?: string; // You can still customize height if needed
}

const ListSkeleton: React.FC<ListSkeletonProps> = ({ count = 5, itemHeight = 'h-10' }) => {
  const skeletonItems = Array.from({ length: count });

  return (
    <div className="space-y-4">
      {skeletonItems.map((_, index) => (
        // Use your imported Skeleton component
        <Skeleton
          key={index}
          className={`w-full ${itemHeight}`} // Apply itemHeight to the Skeleton component
        />
      ))}
    </div>
  );
}

export default ListSkeleton;
```

## `ProviderDetailSkeleton.tsx`

```ts
// components/skeletons/ProviderDetailSkeleton.tsx
"use client";

import React from 'react';
import { Skeleton } from "@/components/ui/skeleton"; // Assuming you have Skeleton component

// A skeleton tailored for the provider details layout
const ProviderDetailSkeleton: React.FC = () => {
  return (
    <div className="space-y-6 p-6 border rounded-md">
      {/* Header area placeholder */}
      <div className="flex justify-between items-center mb-6">
        <Skeleton className="h-8 w-72" /> {/* Provider Name */}
        <Skeleton className="h-10 w-32" /> {/* Edit Button */}
      </div>

      {/* Details Cards Placeholder */}
      <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
        {/* Contact Info Card Skeleton */}
        <div className="space-y-4">
          <Skeleton className="h-6 w-48" /> {/* Card Title */}
          <div className="space-y-2">
            <Skeleton className="h-5 w-64" /> {/* Contact Person */}
            <Skeleton className="h-5 w-56" /> {/* E
```

## `PaginationControls.tsx`

```ts
// components/Table/PaginationControls.tsx
"use client";

import { ArrowLeftIcon, ArrowRightIcon } from '@heroicons/react/24/outline';

export const PaginationControls = ({
  currentPage,
  totalPages,
  onPageChange,
}: {
  currentPage: number;
  totalPages: number;
  onPageChange: (page: number) => void;
}) => {
  return (
    <div className="flex items-center justify-between px-6 py-3 border-t border-gray-200">
      <div className="flex-1 flex justify-between sm:hidden">
        <button
          disabled={currentPage === 1}
          onClick={() => onPageChange(currentPage - 1)}
          className="relative inline-flex items-center px-4 py-2 text-sm font-medium text-gray-700 bg-white hover:bg-gray-50"
        >
          Prethodna
        </button>
        <button
          disabled={currentPage === totalPages}
          onClick={() => onPageChange(currentPage + 1)}
          className="relative inline-flex items-center px-4 py-2 ml-3 text-sm font-medium text-gray-700 bg-white ho
```

## `TableHeader.tsx`

```ts
// components/Table/TableHeader.tsx
"use client";

import { ChevronUpDownIcon, ChevronUpIcon, ChevronDownIcon } from '@heroicons/react/24/outline';

export const TableHeader = ({
  columnKey,
  label,
  sortable,
  sortConfig,
  onSort,
}: {
  columnKey: string;
  label: string;
  sortable?: boolean;
  sortConfig?: { key: string; direction: 'asc' | 'desc' };
  onSort?: (key: string) => void;
}) => {
  const canSort = sortable && onSort;
  const sortDirection = sortConfig?.key === columnKey ? sortConfig.direction : null;

  return (
    <th
      scope="col"
      className={`px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider ${
        canSort ? 'cursor-pointer hover:bg-gray-50' : ''
      }`}
      onClick={canSort ? () => onSort(columnKey) : undefined}
    >
      <div className="flex items-center">
        {label}
        {canSort && (
          <span className="ml-2">
            {!sortDirection && <ChevronUpDownIcon className="h-4 w-4" />}
            {
```

## `portal-toasty.tsx`

```ts
// Path: components/toast/portal-toasty.tsx
'use client';

import { useToast } from './toast-context';
import { createPortal } from 'react-dom';
import { useEffect, useState } from 'react';

export function PortalToasty() {
  const { toasts, removeToast } = useToast();
  const [isMounted, setIsMounted] = useState(false);

  useEffect(() => {
    setIsMounted(true);
    return () => setIsMounted(false);
  }, []);

  if (!isMounted) {
    return null;
  }

  // Make sure the portal returns a single element
  return createPortal(
    <div className="fixed top-4 right-4 z-50 flex flex-col gap-2">
      {toasts.map((toast) => (
        <div
          key={toast.id}
          className={`py-2 px-4 rounded-md shadow-md ${
            toast.isError
              ? 'bg-red-500 text-white'
              : 'bg-green-500 text-white'
          } flex items-center justify-between`}
          role="alert"
        >
          <span>{toast.message}</span>
          <button
            onClick={() => re
```

## `toast-context.tsx`

```ts
// Path: components/toast/toast-context.tsx
'use client';

import React, { createContext, useState, useContext, ReactNode } from 'react';

type ToastType = {
  message: string;
  isError: boolean;
  id: number;
};

type ToastContextType = {
  toasts: ToastType[];
  showToastMessage: (message: string, isError: boolean) => void;
  removeToast: (id: number) => void;
};

const ToastContext = createContext<ToastContextType | undefined>(undefined);

export function ToastProvider({ children }: { children: ReactNode }) {
  const [toasts, setToasts] = useState<ToastType[]>([]);
  const [nextId, setNextId] = useState(1);

  const showToastMessage = (message: string, isError: boolean) => {
    const id = nextId;
    setNextId(id + 1);
    setToasts((prev) => [...prev, { message, isError, id }]);
    
    // Auto-remove after 5 seconds
    setTimeout(() => {
      removeToast(id);
    }, 5000);
  };

  const removeToast = (id: number) => {
    setToasts((prev) => prev.filter((toast) => toast.id !=
```

## `alert.tsx`

```ts
import * as React from "react"
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"

const alertVariants = cva(
  "relative w-full rounded-lg border px-4 py-3 text-sm [&>svg+div]:translate-y-[-3px] [&>svg]:absolute [&>svg]:left-4 [&>svg]:top-4 [&>svg]:text-foreground [&>svg~*]:pl-7",
  {
    variants: {
      variant: {
        default: "bg-background text-foreground",
        destructive:
          "border-destructive/50 text-destructive dark:border-destructive [&>svg]:text-destructive",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
)

const Alert = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement> & VariantProps<typeof alertVariants>
>(({ className, variant, ...props }, ref) => (
  <div
    ref={ref}
    role="alert"
    className={cn(alertVariants({ variant }), className)}
    {...props}
  />
))
Alert.displayName = "Alert"

const AlertTitle = React.forwardRef<
  HTMLParagraphElement
```

## `alert-dialog.tsx`

```ts
"use client"

import * as React from "react"
import * as AlertDialogPrimitive from "@radix-ui/react-alert-dialog"

import { cn } from "@/lib/utils"
import { buttonVariants } from "@/components/ui/button"

const AlertDialog = AlertDialogPrimitive.Root

const AlertDialogTrigger = AlertDialogPrimitive.Trigger

const AlertDialogPortal = AlertDialogPrimitive.Portal

const AlertDialogOverlay = React.forwardRef<
  React.ElementRef<typeof AlertDialogPrimitive.Overlay>,
  React.ComponentPropsWithoutRef<typeof AlertDialogPrimitive.Overlay>
>(({ className, ...props }, ref) => (
  <AlertDialogPrimitive.Overlay
    className={cn(
      "fixed inset-0 z-50 bg-black/80 data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0",
      className
    )}
    {...props}
    ref={ref}
  />
))
AlertDialogOverlay.displayName = AlertDialogPrimitive.Overlay.displayName

const AlertDialogContent = React.forwardRef<
  React.ElementRef<typeof AlertDial
```

## `avatar.tsx`

```ts
"use client"

import * as React from "react"
import * as AvatarPrimitive from "@radix-ui/react-avatar"

import { cn } from "@/lib/utils"

const Avatar = React.forwardRef<
  React.ElementRef<typeof AvatarPrimitive.Root>,
  React.ComponentPropsWithoutRef<typeof AvatarPrimitive.Root>
>(({ className, ...props }, ref) => (
  <AvatarPrimitive.Root
    ref={ref}
    className={cn(
      "relative flex h-10 w-10 shrink-0 overflow-hidden rounded-full",
      className
    )}
    {...props}
  />
))
Avatar.displayName = AvatarPrimitive.Root.displayName

const AvatarImage = React.forwardRef<
  React.ElementRef<typeof AvatarPrimitive.Image>,
  React.ComponentPropsWithoutRef<typeof AvatarPrimitive.Image>
>(({ className, ...props }, ref) => (
  <AvatarPrimitive.Image
    ref={ref}
    className={cn("aspect-square h-full w-full", className)}
    {...props}
  />
))
AvatarImage.displayName = AvatarPrimitive.Image.displayName

const AvatarFallback = React.forwardRef<
  React.ElementRef<typeof AvatarPrimi
```

## `badge.tsx`

```ts
import * as React from "react"
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"

const badgeVariants = cva(
  "inline-flex items-center rounded-md border px-2.5 py-0.5 text-xs font-semibold transition-colors focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2",
  {
    variants: {
      variant: {
        default:
          "border-transparent bg-primary text-primary-foreground shadow hover:bg-primary/80",
        secondary:
          "border-transparent bg-secondary text-secondary-foreground hover:bg-secondary/80",
        destructive:
          "border-transparent bg-destructive text-destructive-foreground shadow hover:bg-destructive/80",
        outline: "text-foreground",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
)

export interface BadgeProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof badgeVariants> {}

function Badge({ className, variant, ...props }: B
```

## `button.tsx`

```ts
import * as React from "react"
import { Slot } from "@radix-ui/react-slot"
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"

const buttonVariants = cva(
  "inline-flex items-center justify-center gap-2 whitespace-nowrap rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50 [&_svg]:pointer-events-none [&_svg]:size-4 [&_svg]:shrink-0",
  {
    variants: {
      variant: {
        default:
          "bg-primary text-primary-foreground shadow hover:bg-primary/90",
        destructive:
          "bg-destructive text-destructive-foreground shadow-sm hover:bg-destructive/90",
        outline:
          "border border-input bg-background shadow-sm hover:bg-accent hover:text-accent-foreground",
        secondary:
          "bg-secondary text-secondary-foreground shadow-sm hover:bg-secondary/80",
        ghost: "hover:bg-accent hov
```

## `calendar.tsx`

```ts
"use client"

import * as React from "react"
import { DayPicker } from "react-day-picker"

import { cn } from "@/lib/utils"
import { buttonVariants } from "@/components/ui/button"
import { ChevronLeftIcon, ChevronRightIcon } from "@radix-ui/react-icons"

export type CalendarProps = React.ComponentProps<typeof DayPicker>

function Calendar({
  className,
  classNames,
  showOutsideDays = true,
  ...props
}: CalendarProps) {
  return (
    <DayPicker
      showOutsideDays={showOutsideDays}
      className={cn("p-3", className)}
      classNames={{
        months: "flex flex-col sm:flex-row space-y-4 sm:space-x-4 sm:space-y-0",
        month: "space-y-4",
        caption: "flex justify-center pt-1 relative items-center",
        caption_label: "text-sm font-medium",
        nav: "space-x-1 flex items-center",
        nav_button: cn(
          buttonVariants({ variant: "outline" }),
          "h-7 w-7 bg-transparent p-0 opacity-50 hover:opacity-100"
        ),
        nav_button_previous: 
```

## `card.tsx`

```ts
import * as React from "react"

import { cn } from "@/lib/utils"

const Card = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={cn(
      "rounded-xl border bg-card text-card-foreground shadow",
      className
    )}
    {...props}
  />
))
Card.displayName = "Card"

const CardHeader = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={cn("flex flex-col space-y-1.5 p-6", className)}
    {...props}
  />
))
CardHeader.displayName = "CardHeader"

const CardTitle = React.forwardRef<
  HTMLParagraphElement,
  React.HTMLAttributes<HTMLHeadingElement>
>(({ className, ...props }, ref) => (
  <h3
    ref={ref}
    className={cn("font-semibold leading-none tracking-tight", className)}
    {...props}
  />
))
CardTitle.displayName = "CardTitle"

const CardDescription = React.forwardRef<
  HTMLParagraphE
```

## `checkbox.tsx`

```ts
"use client"

import * as React from "react"
import * as CheckboxPrimitive from "@radix-ui/react-checkbox"
import { cn } from "@/lib/utils"
import { CheckIcon } from "@radix-ui/react-icons"

const Checkbox = React.forwardRef<
  React.ElementRef<typeof CheckboxPrimitive.Root>,
  React.ComponentPropsWithoutRef<typeof CheckboxPrimitive.Root>
>(({ className, ...props }, ref) => (
  <CheckboxPrimitive.Root
    ref={ref}
    className={cn(
      "peer h-4 w-4 shrink-0 rounded-sm border border-primary shadow focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:cursor-not-allowed disabled:opacity-50 data-[state=checked]:bg-primary data-[state=checked]:text-primary-foreground",
      className
    )}
    {...props}
  >
    <CheckboxPrimitive.Indicator
      className={cn("flex items-center justify-center text-current")}
    >
      <CheckIcon className="h-4 w-4" />
    </CheckboxPrimitive.Indicator>
  </CheckboxPrimitive.Root>
))
Checkbox.displayName = CheckboxPrimit
```

## `data-table-pagination.tsx`

```ts
// components/ui/data-table-pagination.tsx

import {
  ChevronLeft,
  ChevronRight,
  ChevronsLeft,
  ChevronsRight,
} from "lucide-react";
import { Table } from "@tanstack/react-table";

import { Button } from "@/components/ui/button";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";

interface DataTablePaginationProps<TData> {
  table: Table<TData>;
  totalCount?: number;
  pageCount?: number;
}

export function DataTablePagination<TData>({
  table,
  totalCount,
  pageCount,
}: DataTablePaginationProps<TData>) {
  const calculatedTotalCount = totalCount ?? table.getFilteredRowModel().rows.length;
  const calculatedPageCount = pageCount ?? table.getPageCount();

  return (
    <div className="flex items-center justify-between px-2">
      <div className="flex-1 text-sm text-muted-foreground">
        {calculatedTotalCount > 0 && (
          <div>
            {table.getState().pagination.pageIndex * table.getState().pag
```

## `date-picker.tsx`

```ts
// Path: components/ui/date-picker.tsx

"use client";

import React from 'react';
import { Button } from "@/components/ui/button";
import { Calendar } from "@/components/ui/calendar";
import { Popover, PopoverContent, PopoverTrigger } from "@/components/ui/popover";
import { CalendarIcon } from "lucide-react";
import { format } from "date-fns";

interface DatePickerProps {
    name: string;
    date: Date | undefined;
    setDate: (date: Date | undefined) => void;
    // Možda dodati disabled, placeholder, itd.
}

// Ovo je samo placeholder. Moracete implementirati pravu DatePicker komponentu
// koja koristi Popover i Calendar i upravlja stanjem datuma, ili koristiti inline kao u ActivityLog.tsx
export function DatePicker({ name, date, setDate }: DatePickerProps) {
     // Ova implementacija je Client Komponenta jer koristi hooks i interakciju
     // Ali Server Komponenta page.tsx ne moze da upravlja njenim stanjem direktno.
     // Na Server Komponenta stranici, date picker ce raditi
```

## `date-range.tsx`

```ts
// components/ui/date-range.tsx
"use client";

import * as React from "react";
import { CalendarIcon } from "lucide-react";
import { addDays, format } from "date-fns";
import { DateRange as DateRangePrimitive } from "react-day-picker";

import { cn } from "@/lib/utils";
import { Button } from "@/components/ui/button";
import { Calendar } from "@/components/ui/calendar";
import {
  Popover,
  PopoverContent,
  PopoverTrigger,
} from "@/components/ui/popover";

interface DateRangeProps {
  className?: string;
  dateRange: DateRangePrimitive;
  onUpdate: (dateRange: DateRangePrimitive) => void;
  align?: "start" | "center" | "end";
}

export function DateRange({
  className,
  dateRange,
  onUpdate,
  align = "start",
}: DateRangeProps) {
  const [isOpen, setIsOpen] = React.useState(false);

  const handleSelect = (range: DateRangePrimitive | undefined) => {
    if (range) {
      onUpdate(range);
      setIsOpen(false);
    }
  };

  // Predefined date ranges
  const handleLastWeek = () 
```

## `dialog.tsx`

```ts
"use client"

import * as React from "react"
import * as DialogPrimitive from "@radix-ui/react-dialog"
import { Cross2Icon } from "@radix-ui/react-icons"

import { cn } from "@/lib/utils"

const Dialog = DialogPrimitive.Root

const DialogTrigger = DialogPrimitive.Trigger

const DialogPortal = DialogPrimitive.Portal

const DialogClose = DialogPrimitive.Close

const DialogOverlay = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Overlay>,
  React.ComponentPropsWithoutRef<typeof DialogPrimitive.Overlay>
>(({ className, ...props }, ref) => (
  <DialogPrimitive.Overlay
    ref={ref}
    className={cn(
      "fixed inset-0 z-50 bg-black/80  data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0",
      className
    )}
    {...props}
  />
))
DialogOverlay.displayName = DialogPrimitive.Overlay.displayName

const DialogContent = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Content>,
  React.ComponentPro
```

## `dropdown-menu.tsx`

```ts
"use client"

import * as React from "react"
import * as DropdownMenuPrimitive from "@radix-ui/react-dropdown-menu"
import {
  CheckIcon,
  ChevronRightIcon,
  DotFilledIcon,
} from "@radix-ui/react-icons"

import { cn } from "@/lib/utils"

const DropdownMenu = DropdownMenuPrimitive.Root

const DropdownMenuTrigger = DropdownMenuPrimitive.Trigger

const DropdownMenuGroup = DropdownMenuPrimitive.Group

const DropdownMenuPortal = DropdownMenuPrimitive.Portal

const DropdownMenuSub = DropdownMenuPrimitive.Sub

const DropdownMenuRadioGroup = DropdownMenuPrimitive.RadioGroup

const DropdownMenuSubTrigger = React.forwardRef<
  React.ElementRef<typeof DropdownMenuPrimitive.SubTrigger>,
  React.ComponentPropsWithoutRef<typeof DropdownMenuPrimitive.SubTrigger> & {
    inset?: boolean
  }
>(({ className, inset, children, ...props }, ref) => (
  <DropdownMenuPrimitive.SubTrigger
    ref={ref}
    className={cn(
      "flex cursor-default select-none items-center rounded-sm px-2 py-1.5 text-sm outl
```

## `form.tsx`

```ts
"use client"

import * as React from "react"
import * as LabelPrimitive from "@radix-ui/react-label"
import { Slot } from "@radix-ui/react-slot"
import {
  Controller,
  ControllerProps,
  FieldPath,
  FieldValues,
  FormProvider,
  useFormContext,
} from "react-hook-form"

import { cn } from "@/lib/utils"
import { Label } from "@/components/ui/label"

const Form = FormProvider

type FormFieldContextValue<
  TFieldValues extends FieldValues = FieldValues,
  TName extends FieldPath<TFieldValues> = FieldPath<TFieldValues>
> = {
  name: TName
}

const FormFieldContext = React.createContext<FormFieldContextValue>(
  {} as FormFieldContextValue
)

const FormField = <
  TFieldValues extends FieldValues = FieldValues,
  TName extends FieldPath<TFieldValues> = FieldPath<TFieldValues>
>({
  ...props
}: ControllerProps<TFieldValues, TName>) => {
  return (
    <FormFieldContext.Provider value={{ name: props.name }}>
      <Controller {...props} />
    </FormFieldContext.Provider>
  )
}

const us
```

## `heading.tsx`

```ts
///components/ui/heading.tsx

"use client";

import React from "react";
import { cn } from "@/lib/utils";

interface HeadingProps {
  title: string;
  description?: string;
  className?: string;
  titleClassName?: string;
  descriptionClassName?: string;
  children?: React.ReactNode;
}

export const Heading = ({
  title,
  description,
  className,
  titleClassName,
  descriptionClassName,
  children,
}: HeadingProps) => {
  return (
    <div className={cn("flex items-center justify-between", className)}>
      <div>
        <h2 className={cn("text-3xl font-bold tracking-tight", titleClassName)}>
          {title}
        </h2>
        {description && (
          <p
            className={cn(
              "text-sm text-muted-foreground mt-1",
              descriptionClassName
            )}
          >
            {description}
          </p>
        )}
      </div>
      {children}
    </div>
  );
};
```

## `input.tsx`

```ts
import * as React from "react";

import { cn } from "@/lib/utils";

const Input = React.forwardRef<
  HTMLInputElement,
  React.InputHTMLAttributes<HTMLInputElement>
>(({ className, type, ...props }, ref) => {
  return (
    <input
      type={type}
      className={cn(
        "flex h-9 w-full rounded-md border border-input bg-transparent px-3 py-1 text-sm shadow-sm transition-colors file:border-0 file:bg-transparent file:text-sm file:font-medium file:text-foreground placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:cursor-not-allowed disabled:opacity-50",
        className
      )}
      ref={ref}
      {...props}
    />
  );
});
Input.displayName = "Input";

export { Input };

```

## `label.tsx`

```ts
"use client"

import * as React from "react"
import * as LabelPrimitive from "@radix-ui/react-label"
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"

const labelVariants = cva(
  "text-sm font-medium leading-none peer-disabled:cursor-not-allowed peer-disabled:opacity-70"
)

const Label = React.forwardRef<
  React.ElementRef<typeof LabelPrimitive.Root>,
  React.ComponentPropsWithoutRef<typeof LabelPrimitive.Root> &
    VariantProps<typeof labelVariants>
>(({ className, ...props }, ref) => (
  <LabelPrimitive.Root
    ref={ref}
    className={cn(labelVariants(), className)}
    {...props}
  />
))
Label.displayName = LabelPrimitive.Root.displayName

export { Label }

```

## `navigation-menu.tsx`

```ts
//components/ui/navigation-menu.tsx

import * as React from "react"
import * as NavigationMenuPrimitive from "@radix-ui/react-navigation-menu"
import { cva } from "class-variance-authority"
import { ChevronDown } from "lucide-react"

import { cn } from "@/lib/utils"

const NavigationMenu = React.forwardRef<
  React.ElementRef<typeof NavigationMenuPrimitive.Root>,
  React.ComponentPropsWithoutRef<typeof NavigationMenuPrimitive.Root>
>(({ className, children, ...props }, ref) => (
  <NavigationMenuPrimitive.Root
    ref={ref}
    className={cn(
      "relative z-10 flex max-w-max flex-1 items-center justify-center",
      className
    )}
    {...props}
  >
    {children}
    <NavigationMenuViewport />
  </NavigationMenuPrimitive.Root>
))
NavigationMenu.displayName = NavigationMenuPrimitive.Root.displayName

const NavigationMenuList = React.forwardRef<
  React.ElementRef<typeof NavigationMenuPrimitive.List>,
  React.ComponentPropsWithoutRef<typeof NavigationMenuPrimitive.List>
>(({ class
```

## `pagination.tsx`

```ts
import * as React from "react"
import { cn } from "@/lib/utils"
import { ButtonProps, buttonVariants } from "@/components/ui/button"
import { ChevronLeftIcon, ChevronRightIcon, DotsHorizontalIcon } from "@radix-ui/react-icons"

const Pagination = ({ className, ...props }: React.ComponentProps<"nav">) => (
  <nav
    role="navigation"
    aria-label="pagination"
    className={cn("mx-auto flex w-full justify-center", className)}
    {...props}
  />
)
Pagination.displayName = "Pagination"

const PaginationContent = React.forwardRef<
  HTMLUListElement,
  React.ComponentProps<"ul">
>(({ className, ...props }, ref) => (
  <ul
    ref={ref}
    className={cn("flex flex-row items-center gap-1", className)}
    {...props}
  />
))
PaginationContent.displayName = "PaginationContent"

const PaginationItem = React.forwardRef<
  HTMLLIElement,
  React.ComponentProps<"li">
>(({ className, ...props }, ref) => (
  <li ref={ref} className={cn("", className)} {...props} />
))
PaginationItem.displayName
```

## `popover.tsx`

```ts
"use client"

import * as React from "react"
import * as PopoverPrimitive from "@radix-ui/react-popover"

import { cn } from "@/lib/utils"

const Popover = PopoverPrimitive.Root

const PopoverTrigger = PopoverPrimitive.Trigger

const PopoverAnchor = PopoverPrimitive.Anchor

const PopoverContent = React.forwardRef<
  React.ElementRef<typeof PopoverPrimitive.Content>,
  React.ComponentPropsWithoutRef<typeof PopoverPrimitive.Content>
>(({ className, align = "center", sideOffset = 4, ...props }, ref) => (
  <PopoverPrimitive.Portal>
    <PopoverPrimitive.Content
      ref={ref}
      align={align}
      sideOffset={sideOffset}
      className={cn(
        "z-50 w-72 rounded-md border bg-popover p-4 text-popover-foreground shadow-md outline-none data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-fro
```

## `progress.tsx`

```ts
"use client"

import * as React from "react"
import * as ProgressPrimitive from "@radix-ui/react-progress"

import { cn } from "@/lib/utils"

const Progress = React.forwardRef<
  React.ElementRef<typeof ProgressPrimitive.Root>,
  React.ComponentPropsWithoutRef<typeof ProgressPrimitive.Root>
>(({ className, value, ...props }, ref) => (
  <ProgressPrimitive.Root
    ref={ref}
    className={cn(
      "relative h-2 w-full overflow-hidden rounded-full bg-primary/20",
      className
    )}
    {...props}
  >
    <ProgressPrimitive.Indicator
      className="h-full w-full flex-1 bg-primary transition-all"
      style={{ transform: `translateX(-${100 - (value || 0)}%)` }}
    />
  </ProgressPrimitive.Root>
))
Progress.displayName = ProgressPrimitive.Root.displayName

export { Progress }

```

## `radio-group.tsx`

```ts
"use client"

import * as React from "react"
import * as RadioGroupPrimitive from "@radix-ui/react-radio-group"
import { cn } from "@/lib/utils"
import { DotFilledIcon } from "@radix-ui/react-icons"

const RadioGroup = React.forwardRef<
  React.ElementRef<typeof RadioGroupPrimitive.Root>,
  React.ComponentPropsWithoutRef<typeof RadioGroupPrimitive.Root>
>(({ className, ...props }, ref) => {
  return (
    <RadioGroupPrimitive.Root
      className={cn("grid gap-2", className)}
      {...props}
      ref={ref}
    />
  )
})
RadioGroup.displayName = RadioGroupPrimitive.Root.displayName

const RadioGroupItem = React.forwardRef<
  React.ElementRef<typeof RadioGroupPrimitive.Item>,
  React.ComponentPropsWithoutRef<typeof RadioGroupPrimitive.Item>
>(({ className, ...props }, ref) => {
  return (
    <RadioGroupPrimitive.Item
      ref={ref}
      className={cn(
        "aspect-square h-4 w-4 rounded-full border border-primary text-primary shadow focus:outline-none focus-visible:ring-1 focus-v
```

## `scroll-area.tsx`

```ts
"use client"

import * as React from "react"
import * as ScrollAreaPrimitive from "@radix-ui/react-scroll-area"

import { cn } from "@/lib/utils"

const ScrollArea = React.forwardRef<
  React.ElementRef<typeof ScrollAreaPrimitive.Root>,
  React.ComponentPropsWithoutRef<typeof ScrollAreaPrimitive.Root>
>(({ className, children, ...props }, ref) => (
  <ScrollAreaPrimitive.Root
    ref={ref}
    className={cn("relative overflow-hidden", className)}
    {...props}
  >
    <ScrollAreaPrimitive.Viewport className="h-full w-full rounded-[inherit]">
      {children}
    </ScrollAreaPrimitive.Viewport>
    <ScrollBar />
    <ScrollAreaPrimitive.Corner />
  </ScrollAreaPrimitive.Root>
))
ScrollArea.displayName = ScrollAreaPrimitive.Root.displayName

const ScrollBar = React.forwardRef<
  React.ElementRef<typeof ScrollAreaPrimitive.ScrollAreaScrollbar>,
  React.ComponentPropsWithoutRef<typeof ScrollAreaPrimitive.ScrollAreaScrollbar>
>(({ className, orientation = "vertical", ...props }, ref) => (

```

## `select.tsx`

```ts
// components/ui/select.tsx

"use client"

import * as React from "react"
import {
  CaretSortIcon,
  CheckIcon,
  ChevronDownIcon,
  ChevronUpIcon,
} from "@radix-ui/react-icons"
import * as SelectPrimitive from "@radix-ui/react-select"

import { cn } from "@/lib/utils"

const Select = SelectPrimitive.Root

const SelectGroup = SelectPrimitive.Group

const SelectValue = SelectPrimitive.Value

const SelectTrigger = React.forwardRef<
  React.ElementRef<typeof SelectPrimitive.Trigger>,
  React.ComponentPropsWithoutRef<typeof SelectPrimitive.Trigger>
>(({ className, children, ...props }, ref) => (
  <SelectPrimitive.Trigger
    ref={ref}
    className={cn(
      "flex h-9 w-full items-center justify-between whitespace-nowrap rounded-md border border-input bg-transparent px-3 py-2 text-sm shadow-sm ring-offset-background placeholder:text-muted-foreground focus:outline-none focus:ring-1 focus:ring-ring disabled:cursor-not-allowed disabled:opacity-50 [&>span]:line-clamp-1",
      className
  
```

## `separator.tsx`

```ts
"use client"

import * as React from "react"
import * as SeparatorPrimitive from "@radix-ui/react-separator"

import { cn } from "@/lib/utils"

const Separator = React.forwardRef<
  React.ElementRef<typeof SeparatorPrimitive.Root>,
  React.ComponentPropsWithoutRef<typeof SeparatorPrimitive.Root>
>(
  (
    { className, orientation = "horizontal", decorative = true, ...props },
    ref
  ) => (
    <SeparatorPrimitive.Root
      ref={ref}
      decorative={decorative}
      orientation={orientation}
      className={cn(
        "shrink-0 bg-border",
        orientation === "horizontal" ? "h-[1px] w-full" : "h-full w-[1px]",
        className
      )}
      {...props}
    />
  )
)
Separator.displayName = SeparatorPrimitive.Root.displayName

export { Separator }

```

## `skeleton.tsx`

```ts
//components/ui/skeleton.tsx

import { cn } from "@/lib/utils"

function Skeleton({
  className,
  ...props
}: React.HTMLAttributes<HTMLDivElement>) {
  return (
    <div
      className={cn("animate-pulse rounded-md bg-primary/10", className)}
      {...props}
    />
  )
}

export { Skeleton }

```

## `sonner.tsx`

```ts
"use client"

import { useTheme } from "next-themes"
import { Toaster as Sonner } from "sonner"

type ToasterProps = React.ComponentProps<typeof Sonner>

const Toaster = ({ ...props }: ToasterProps) => {
  const { theme = "system" } = useTheme()

  return (
    <Sonner
      theme={theme as ToasterProps["theme"]}
      className="toaster group"
      toastOptions={{
        classNames: {
          toast:
            "group toast group-[.toaster]:bg-background group-[.toaster]:text-foreground group-[.toaster]:border-border group-[.toaster]:shadow-lg",
          description: "group-[.toast]:text-muted-foreground",
          actionButton:
            "group-[.toast]:bg-primary group-[.toast]:text-primary-foreground",
          cancelButton:
            "group-[.toast]:bg-muted group-[.toast]:text-muted-foreground",
        },
      }}
      {...props}
    />
  )
}

export { Toaster }

```

## `spinner.tsx`

```ts
// components/ui/spinner.tsx
import { Loader2 } from 'lucide-react'; // Pretpostavka da koristiš lucide-react za ikonice
import { cn } from '@/lib/utils'; // Pretpostavka da imaš utility funkciju za spajanje klasa (npr. iz Shadcn UI setupa)
import React from 'react'; // Eksplicitno uvezi React

interface SpinnerProps {
  className?: string; // Omogućava dodavanje dodatnih CSS klasa
}

/**
 * A simple spinner component using Lucide React Loader2 icon.
 * Displays a spinning loading indicator.
 */
export const Spinner: React.FC<SpinnerProps> = ({ className }) => {
  return (
    // Koristi cn za spajanje podrazumevanih klasa (animate-spin) sa prosleđenim klasama
    <Loader2 className={cn('h-4 w-4 animate-spin', className)} />
  );
};

// Ako NE koristiš `lucide-react` i `@/lib/utils/cn`, možeš koristiti jednostavniju SVG verziju:
/*
export const Spinner: React.FC<SpinnerProps> = ({ className }) => {
  return (
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="24"
      heig
```

## `switch.tsx`

```ts
"use client"

import * as React from "react"
import * as SwitchPrimitives from "@radix-ui/react-switch"

import { cn } from "@/lib/utils"

const Switch = React.forwardRef<
  React.ElementRef<typeof SwitchPrimitives.Root>,
  React.ComponentPropsWithoutRef<typeof SwitchPrimitives.Root>
>(({ className, ...props }, ref) => (
  <SwitchPrimitives.Root
    className={cn(
      "peer inline-flex h-5 w-9 shrink-0 cursor-pointer items-center rounded-full border-2 border-transparent shadow-sm transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 focus-visible:ring-offset-background disabled:cursor-not-allowed disabled:opacity-50 data-[state=checked]:bg-primary data-[state=unchecked]:bg-input",
      className
    )}
    {...props}
    ref={ref}
  >
    <SwitchPrimitives.Thumb
      className={cn(
        "pointer-events-none block h-4 w-4 rounded-full bg-background shadow-lg ring-0 transition-transform data-[state=checked]:translate-
```

## `table.tsx`

```ts
//components/ui/table.tsx
import * as React from "react";

import { cn } from "@/lib/utils";

const Table = React.forwardRef<
  HTMLTableElement,
  React.HTMLAttributes<HTMLTableElement>
>(({ className, children, ...props }, ref) => (
  <div className="relative w-full overflow-auto">
    <table
      ref={ref}
      className={cn("w-full caption-bottom text-sm", className)}
      {...props}
    >{children}</table>
  </div>
));
Table.displayName = "Table";

const TableHeader = React.forwardRef<
  HTMLTableSectionElement,
  React.HTMLAttributes<HTMLTableSectionElement>
>(({ className, children, ...props }, ref) => (
  <thead ref={ref} className={cn("[&_tr]:border-b", className)} {...props}>{children}</thead>
));
TableHeader.displayName = "TableHeader";

const TableBody = React.forwardRef<
  HTMLTableSectionElement,
  React.HTMLAttributes<HTMLTableSectionElement>
>(({ className, children, ...props }, ref) => (
  <tbody
    ref={ref}
    className={cn("[&_tr:last-child]:border-0", classNam
```

## `tabs.tsx`

```ts
"use client"

import * as React from "react"
import * as TabsPrimitive from "@radix-ui/react-tabs"

import { cn } from "@/lib/utils"

const Tabs = TabsPrimitive.Root

const TabsList = React.forwardRef<
  React.ElementRef<typeof TabsPrimitive.List>,
  React.ComponentPropsWithoutRef<typeof TabsPrimitive.List>
>(({ className, ...props }, ref) => (
  <TabsPrimitive.List
    ref={ref}
    className={cn(
      "inline-flex h-9 items-center justify-center rounded-lg bg-muted p-1 text-muted-foreground",
      className
    )}
    {...props}
  />
))
TabsList.displayName = TabsPrimitive.List.displayName

const TabsTrigger = React.forwardRef<
  React.ElementRef<typeof TabsPrimitive.Trigger>,
  React.ComponentPropsWithoutRef<typeof TabsPrimitive.Trigger>
>(({ className, ...props }, ref) => (
  <TabsPrimitive.Trigger
    ref={ref}
    className={cn(
      "inline-flex items-center justify-center whitespace-nowrap rounded-md px-3 py-1 text-sm font-medium ring-offset-background transition-all focus-v
```

## `textarea.tsx`

```ts
import * as React from "react"

import { cn } from "@/lib/utils"

const Textarea = React.forwardRef<
  HTMLTextAreaElement,
  React.ComponentProps<"textarea">
>(({ className, ...props }, ref) => {
  return (
    <textarea
      className={cn(
        "flex min-h-[60px] w-full rounded-md border border-input bg-transparent px-3 py-2 text-base shadow-sm placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:cursor-not-allowed disabled:opacity-50 md:text-sm",
        className
      )}
      ref={ref}
      {...props}
    />
  )
})
Textarea.displayName = "Textarea"

export { Textarea }

```

## `timeline.tsx`

```ts
// components/ui/timeline.tsx

import * as React from "react";
import { cn } from "@/lib/utils";

const Timeline = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={cn("relative", className)}
    {...props}
  />
));
Timeline.displayName = "Timeline";

const TimelineItem = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={cn("flex mb-6 last:mb-0", className)}
    {...props}
  />
));
TimelineItem.displayName = "TimelineItem";

const TimelineSeparator = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={cn("flex flex-col items-center", className)}
    {...props}
  />
));
TimelineSeparator.displayName = "TimelineSeparator";

const TimelineDot = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttribu
```

## `toast1.tsx`

```ts
// Path: components/ui/toast.tsx
"use client";

import * as React from "react";
import * as ToastPrimitives from "@radix-ui/react-toast";
import { cva, type VariantProps } from "class-variance-authority";
import { X } from "lucide-react";

import { cn } from "@/lib/utils";

const ToastProvider = ToastPrimitives.Provider;

const ToastViewport = React.forwardRef<
  React.ElementRef<typeof ToastPrimitives.Viewport>,
  React.ComponentPropsWithoutRef<typeof ToastPrimitives.Viewport>
>(({ className, ...props }, ref) => (
  <ToastPrimitives.Viewport
    ref={ref}
    className={cn(
      "fixed top-0 z-[100] flex max-h-screen w-full flex-col-reverse p-4 sm:bottom-0 sm:right-0 sm:top-auto sm:flex-col md:max-w-[420px]",
      className
    )}
    {...props}
  />
));
ToastViewport.displayName = ToastPrimitives.Viewport.displayName;

const toastVariants = cva(
  "group pointer-events-auto relative flex w-full items-center justify-between space-x-4 overflow-hidden rounded-md border p-6 pr-8 shadow
```

## `toaster1.tsx`

```ts
// Path: components/ui/toaster.tsx
"use client";

// ISPRAVITE OVU PUTANJU da pokazuje na vaš use-toast.tsx fajl
import { useToast } from "@/components/ui/use-toast"; // <-- Ispravite OVDJE putanju!

import {
  Toast,
  ToastClose,
  ToastDescription,
  ToastProvider,
  ToastTitle,
  ToastViewport,
} from "@/components/ui/toast"; // Proverite da li putanja do toast.tsx odgovara i OVDE


export function Toaster() {
  const { toasts } = useToast();

  return (
    <ToastProvider>
      <ToastViewport />
      {toasts.map(function ({ id, title, description, action, ...props }) {
        return (
          <Toast key={id} {...props}>
            <div className="grid gap-1">
              {title && <ToastTitle>{title}</ToastTitle>}
              {description && (
                <ToastDescription>{description}</ToastDescription>
              )}
            </div>
            {action}
            <ToastClose />
          </Toast>
        );
      })}
    </ToastProvider>
  );
}
```

## `tooltip.tsx`

```ts
"use client"

import * as React from "react"
import * as TooltipPrimitive from "@radix-ui/react-tooltip"

import { cn } from "@/lib/utils"

const TooltipProvider = TooltipPrimitive.Provider

const Tooltip = TooltipPrimitive.Root

const TooltipTrigger = TooltipPrimitive.Trigger

const TooltipContent = React.forwardRef<
  React.ElementRef<typeof TooltipPrimitive.Content>,
  React.ComponentPropsWithoutRef<typeof TooltipPrimitive.Content>
>(({ className, sideOffset = 4, ...props }, ref) => (
  <TooltipPrimitive.Portal>
    <TooltipPrimitive.Content
      ref={ref}
      sideOffset={sideOffset}
      className={cn(
        "z-50 overflow-hidden rounded-md bg-primary px-3 py-1.5 text-xs text-primary-foreground animate-in fade-in-0 zoom-in-95 data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=closed]:zoom-out-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 ori
```

## `use-toast1.tsx`

```ts
// Path: components/ui/use-toast.tsx
"use client";

import * as React from "react";

const ACTION_TYPES = {
  ADD_TOAST: "ADD_TOAST",
  UPDATE_TOAST: "UPDATE_TOAST",
  DISMISS_TOAST: "DISMISS_TOAST",
  REMOVE_TOAST: "REMOVE_TOAST",
} as const;

type ActionType = typeof ACTION_TYPES;

type Action =
  | {
      type: ActionType["ADD_TOAST"];
      toast: Toast;
    }
  | {
      type: ActionType["UPDATE_TOAST"];
      toast: Partial<Toast>;
    }
  | {
      type: ActionType["DISMISS_TOAST"];
      toastId?: Toast["id"];
    }
  | {
      type: ACTION_TYPES.REMOVE_TOAST;
      toastId?: Toast["id"];
    };

interface State {
  toasts: Toast[];
}

const TOAST_LIMIT = 1;
const TOAST_REMOVE_DELAY = 1000000;

let count = 0;

function genId() {
  count = (count + 1) % Number.MAX_SAFE_INTEGER;
  return count.toString();
}

type Toast = {
  id: string;
  title?: React.ReactNode;
  description?: React.ReactNode;
  action?: React.ReactNode;
  duration?: number;
  className?: string;
  variant?: "
```

