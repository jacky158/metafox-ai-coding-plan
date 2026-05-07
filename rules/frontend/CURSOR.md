# Cursor — indexing & references (MetaFox frontend)

## Codebase indexing

1. Open this folder (or add it to a multi-root workspace with `backend`).
2. **Cursor Settings → Features:** turn on codebase indexing / semantic features as needed.
3. Use **Command Palette** (`Cmd/Ctrl+Shift+P`) → **Reindex** after large `.cursorignore` changes.
4. **`.cursorignore`** (this repo) skips `node_modules`, `dist`, generated `app/src/bundle-*`, and other heavy trees so indexing stays fast ([ignore files docs](https://cursor.com/docs/context/ignore-files)).
5. **`.cursorindexingignore`** — optional extra excludes for the index only.

## Project rules layout

- **`.cursorrules`** — React/MUI/Metafox conventions at a glance.
- **`.cursor/rules/*.mdc`** — layered rules (e.g. `always/metafox-stack.mdc`, push notification, TS/MUI, sagas).

## React & UI docs (for correct APIs)

- React: https://react.dev  
- TypeScript: https://www.typescriptlang.org/docs/  
- MUI v6: https://mui.com/material-ui/

## Laravel (when changing API contracts)

Backend lives in sibling `backend/` — Laravel 12 docs: https://laravel.com/docs/12.x  

## Installing Cursor

Download **Cursor** from [cursor.com](https://cursor.com).
