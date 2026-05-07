# Metafox frontend — agent context

**Package manager:** `pnpm` (see root `package.json`: `packageManager: pnpm@9.15.4`, Node `>= 20`).

## Workspace layout

- **Root** `@metafox/react`: CLI scripts (`metafox`), app wiring.
- **`packages/*/*`**: Feature and framework code — imports like `@metafox/framework`, `@metafox/core`, `@metafox/ui`, etc. (see `pnpm-workspace.yaml`).
- **`app/`**: Web/admin/installation shells as configured by the Metafox toolchain.

Coding conventions are split across [`.cursor/rules/`](.cursor/rules/): always-on stack rules under `always/`, plus file-scoped rules for TS/TSX and `sagas/`.

## Common commands (run from this repo root)

| Task | Command |
|------|---------|
| Install / bootstrap | `pnpm install` then `pnpm run bootstrap` |
| Dev web (port 3000) | `pnpm run start` |
| Dev AdminCP | `pnpm run start:admincp` |
| Jest | `pnpm run test` |
| Metafox unit tests | `pnpm run unittest` |
| Production build (full) | `pnpm run bundle` |
| Reload dev assets | `pnpm run reload` |

## Related repos in the same workspace

- **Backend** (Laravel API): sibling folder `backend/` — not under this tree.
- **Docker** (Traefik, PHP-FPM, nginx, DBs): sibling folder `docker/`.

See also: [`AGENTS.md`](../backend/AGENTS.md) when changing API contracts.

## Internationalization (i18n)

- **Runtime UI strings**: [`metafox-intl`](packages/framework/metafox-intl/) — `IntlProvider` wraps the app, resolves locale from session `language_id`, preference `userLanguage`, then [`detectBrowserLanguage`](packages/framework/metafox-utils/src/detectBrowserLanguage.ts), then `MFOX_LOCALE`. Uses `react-intl` (`formatMessage`); `manager` exposes `i18n`.
- **Bootstrap phrases**: saga [`metafox-intl/src/sagas/intl.ts`](packages/framework/metafox-intl/src/sagas/intl.ts) loads `core/translation/{group}/auto/{revision}` and merges into Redux `intl.messages`.
- **API alignment**: [`metafox-rest-client` `RestClient`](packages/framework/metafox-rest-client/src/RestClient.tsx) sends **`X-Language`** from the `userLanguage` cookie so Laravel `App::getLocale()` matches the SPA before translation and other localized responses.
- **Date/time**: [`momentLocales.ts`](packages/framework/metafox-intl/src/momentLocales.ts) registers common Moment locales used with `moment.locale(...)` in `IntlProvider`.

## Cursor & indexing

- See [`CURSOR.md`](CURSOR.md) for codebase indexing, `.cursorignore`, and links to React/MUI/Laravel docs.
