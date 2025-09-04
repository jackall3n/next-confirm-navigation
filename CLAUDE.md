# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Next.js navigation guard library that provides confirmation dialogs before navigation events. The library intercepts various navigation methods (router push/replace, browser back/forward, page refresh) and allows developers to conditionally block navigation with user confirmation.

## Commands

### Development
- `bun install` - Install dependencies
- `bun run dev` - Run tsdown in watch mode for development
- `bun run playground` - Start Vite dev server for testing components in playground

### Building
- `bun run build` - Build the library using tsdown

### Testing
- `bun run test` - Run all tests with Vitest
- `bun run test [pattern]` - Run specific tests matching pattern

### Code Quality
- `bun run typecheck` - Type check with TypeScript (no emit)
- Biome is configured for formatting and linting with tab indentation and double quotes

### Release
- `bun run release` - Bump version and publish to npm
- `bun run prepublishOnly` - Automatically runs build before publishing

## Architecture

### Core Components

The library uses a Provider pattern with React Context to manage navigation guards across the application:

1. **NavigationGuardProvider** (`src/components/NavigationGuardProvider.tsx`)
   - Root provider that wraps the application
   - Manages a Map of guard definitions via useRef
   - Coordinates all navigation interception hooks
   - Wraps children with InterceptAppRouterProvider and InterceptPagesRouterProvider

2. **useNavigationGuard Hook** (`src/hooks/useNavigationGuard.ts`)
   - Main consumer API for implementing navigation guards
   - Returns `{ active, accept, reject }` for managing confirmation state
   - Supports both synchronous callbacks and async Promise-based confirmations
   - Registers guards in the provider's Map using a unique ID

### Navigation Interception Hooks

The library intercepts navigation through multiple hooks:

- **useInterceptPopState** - Handles browser back/forward navigation
- **useInterceptPageUnload** - Handles page refresh/close events  
- **useInterceptLinkClicks** - Intercepts anchor tag clicks
- **useInterceptedAppRouter** - Intercepts Next.js App Router navigation
- **useInterceptedPagesRouter** - Intercepts Next.js Pages Router navigation

### Type System

Key types defined in `src/types.ts`:
- `NavigationGuardCallback` - Function that determines if navigation is allowed
- `NavigationGuardParams` - Contains navigation details (to, type)
- `NavigationGuardOptions` - Configuration for guards (enabled, confirm callback)
- `GuardDef` - Internal guard definition with enabled check and callback

### Build Configuration

- **TypeScript**: Configured for ESNext target with strict mode
- **tsdown**: Used for building with entry point at `src/index.ts`
- **Vitest**: Test runner with happy-dom environment
- **Biome**: Code formatter/linter with tab indentation and double quotes

## Project Structure

```
src/
├── index.ts                    # Main exports
├── types.ts                    # Type definitions
├── components/
│   ├── NavigationGuardProvider.tsx
│   ├── NavigationGuardProviderContext.tsx
│   ├── InterceptAppRouterProvider.tsx
│   └── InterceptPagesRouterProvider.tsx
├── hooks/
│   ├── useNavigationGuard.ts
│   ├── useInterceptPopState.ts
│   ├── useInterceptLinkClicks.ts
│   ├── useInterceptedAppRouter.ts
│   ├── useInterceptedPagesRouter.ts
│   ├── useInterceptPageUnload.ts
│   └── useIsomorphicLayoutEffect.ts
└── utils/
    ├── debug.ts
    └── historyAugmentation.tsx

playground/                     # Vite-based development playground
tests/                         # Vitest test files
```

## Dependencies

- Peer dependency: `next@^15.5.2`
- Development uses React 19 with Vite (rolldown-vite)