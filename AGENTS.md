# Repository Guidelines

This document explains how to work on the Wuhr AI VRAM Insight repo in a consistent and reliable way.

## Project Structure & Modules

- `src/app`: Next.js App Router entry, layouts, pages, and API routes.
- `src/components`, `src/contexts`, `src/hooks`, `src/store`: React UI, context, hooks, and Zustand stores.
- `src/lib`, `src/utils`, `src/types`: Core VRAM calculation logic, helpers, and shared types.
- `mcp-server`: Standalone MCP server package (TypeScript) published to npm.
- `public`, `docs`, `scripts`, `issues`: Static assets, documentation, helper scripts, and issue templates.

## Build, Test, and Development

- `npm install` / `npm ci`: Install root dependencies.
- `npm run dev`: Start the Next.js app at `http://localhost:3000`.
- `npm run lint`: Run ESLint using `eslint.config.mjs`.
- `npm run build`: Production build; must succeed before merging.
- `npm test`: Placeholder UI tests (kept green in CI).
- `npm run test:mcp`: Basic MCP client tests (`test-mcp.js`).
- `cd mcp-server && npm install && npm test`: Run MCP server tests.

## Coding Style & Naming

- Language: TypeScript + React function components; Next.js App Router.
- Indent with 2 spaces, use single quotes, and keep imports ordered.
- Prefer small, focused components; reuse utilities in `src/lib` and `src/utils`.
- Use Tailwind CSS utility classes; keep class names consistent with existing patterns.
- File names: kebab-case (`config-presets-panel.tsx`), components in PascalCase (`ConfigPresetsPanel`), variables in camelCase.
- Validate changes with `npm run lint` before committing.

## Testing Guidelines

- Frontend currently has minimal automated tests; add tests for non-trivial logic.
- Place new tests close to the code they cover and keep them fast.
- Use `npm run test:mcp` and `mcp-server` tests when touching MCP-related code.

## Commit & Pull Request Guidelines

- Follow a Conventional Commits style: `feat: ...`, `fix: ...`, `docs: ...`.
- Commits should be small and focused; avoid mixing refactors with feature changes.
- PRs must include a clear description, screenshots for UI changes, and a short “Testing” section (`npm run lint`, `npm run build`, etc.).
- Link related GitHub issues where possible.

## Agent-Specific Instructions

- Prefer simple, explicit implementations over clever ones; avoid large refactors unless requested.
- Keep changes localized, reuse existing patterns and utilities, and respect current architecture and naming.
- Always ensure `npm run lint` and `npm run build` pass locally before suggesting a change.
