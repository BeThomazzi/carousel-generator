# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev       # Start development server
npm run build     # Build for production
npm run start     # Start production server
npm run lint      # Run ESLint
npm run format    # Format code with Prettier
```

## Architecture

**Next.js 16** (App Router) + **React 19** + **TypeScript** + **Tailwind CSS v4**

### Structure
- `src/app/` - Next.js App Router pages and API routes
- `src/app/_components/` - React components (private to app)
- `src/lib/` - Utility functions

### Core Application Flow

**Main page** (`src/app/page.tsx`) composes three panels:
1. **EditorPanel** - Left panel for editing carousel content
2. **PreviewPanel** - Center panel showing live preview
3. **NavigationPanel** - Right panel for slide navigation and export

**State management** (`src/app/_components/carousel/useCarouselState.ts`):
- Custom hook managing `CarouselState` with localStorage persistence
- Discriminated union for slide types: `cover` | `content` | `closing`
- Exposes `updateGlobal`, `updateSlideCount`, `updateSlide` mutators

**Image export** (`src/app/api/export-image/route.ts`):
- Server-side rendering via Puppeteer + Chromium
- Local dev: uses system Chrome (`CHROME_EXECUTABLE_PATH` env var fallback)
- Production (Vercel): uses `@sparticuz/chromium` serverless binary
- Injects state via `window.__INJECTED_CAROUSEL_STATE__` to bypass localStorage 5MB limit
- Renders at 1080x1350 (Instagram portrait ratio)

### Key Patterns

**Utility** (`src/lib/utils.ts`):
```typescript
cn(...inputs) // Merges class names via clsx + tailwind-merge
```

**Styling**:
- Tailwind CSS v4 with OKLCH color space
- Custom fonts via `@theme inline` in `globals.css`
- shadcn/ui components for UI primitives

**Prettier**:
- `prettier-plugin-tailwindcss` configured with custom `cn` function recognition

## Next.js Version Warning

This project uses **Next.js 16** with **Tailwind CSS v4** — both have breaking changes from common tutorials. Consult `node_modules/next/dist/docs/` for current APIs.
