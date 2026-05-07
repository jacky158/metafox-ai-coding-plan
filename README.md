# MetaFox AI Coding Plan

Bộ tài liệu để **AI agent (Cursor, Claude Code, Cline, Codex CLI, ...)** làm việc hiệu quả với codebase MetaFox (Laravel 12 + Octane backend, React 18 + MUI v6 + Redux-Saga frontend).

## File map

```
metafox-workspace/
├── README.md                  ← bạn đang ở đây (chỉ mục + hướng dẫn dùng)
├── AGENTS.md                  ← Bản portable copy-paste cho mọi agent / IDE
├── core-workflow.md           ← System prompt chi tiết cho AI agent (vai trò, workflow, rules)
├── prompt-commands.md         ← Thư viện prompt theo từng nhiệm vụ (22 chương)
├── ai-coding-plan.md          ← Tài liệu lý thuyết: AI agent fundamentals + roadmap học
├── backend/.cursor/           ← Snapshot cursor rules cho backend (đã sync với repo live)
├── frontend/.cursor/          ← Snapshot cursor rules cho frontend (đã sync với repo live)
└── legacy/                    ← Tài liệu PhpFox cũ (chỉ tham khảo, không dùng sinh code)
```

| File | Khi nào dùng | Audience |
|------|-------------|----------|
| `AGENTS.md` | **Copy-paste sang repo / agent khác** — bản gọn portable, đầy đủ context. | AI agent |
| `core-workflow.md` | Bản dài chi tiết hơn AGENTS.md — paste khi cần workflow dài + Don'ts mở rộng. | AI agent |
| `prompt-commands.md` | Khi cần **làm một việc cụ thể** — copy prompt mẫu rồi tinh chỉnh. | Developer + agent |
| `ai-coding-plan.md` | Đọc 1 lần để hiểu lý thuyết AI agent + lộ trình học. | Developer (con người) |
| `backend/.cursor/` & `frontend/.cursor/` | Tham khảo rules đang live trong repo. | AI agent / DevOps |
| `legacy/` | Tra cứu khái niệm thế hệ trước (PhpFox v3/v4). | Maintainer kế thừa |

> **`AGENTS.md` vs `core-workflow.md`:** Cùng nội dung tinh thần. `AGENTS.md` (~150 dòng) là bản portable nhỏ gọn để copy sang bất kỳ chỗ nào (repo mới, system prompt agent SDK, paste đầu chat). `core-workflow.md` (~143 dòng) chi tiết hơn về debugging playbook + feature checklist BE/FE. Nếu chỉ cần 1 file → dùng `AGENTS.md`.

---

## 1. Cách dùng `core-workflow.md`

`core-workflow.md` đóng vai trò **system prompt / role definition**. Nó nói cho agent biết:

- Stack chính xác (Laravel 12 + Octane + Passport + Spatie + l5-repository / React 18 + MUI v6 + Redux-Saga + pnpm + `@metafox/*`).
- Lifecycle đầy đủ của 1 request UI → DB.
- 7 bước workflow (analyze → retrieve → flow → plan → patch → validate → summarize).
- Danh sách Don'ts (không Sanctum, không Tailwind ngoài file đã có, không sửa Octane/Passport bootstrap, ...).
- Output format chuẩn (problem analysis → relevant files → patch → validation checklist → risks).

### 1.1 Trong Cursor IDE

**Cách A — User Rules (global, dùng cho mọi project):**

1. Cmd+Shift+P → "Cursor Settings: Open User Rules".
2. Paste toàn bộ nội dung `core-workflow.md`.
3. Cursor sẽ áp dụng cho mọi chat / Composer / Agent session.

**Cách B — Workspace AGENTS.md (cho repo MetaFox):**

Đã có sẵn — `backend/AGENTS.md` và `frontend/AGENTS.md` trong các repo MetaFox đã reference các rule trong `.cursor/rules/`. `core-workflow.md` là phiên bản **portable** dùng khi chat ở folder không có `.cursor/rules/`.

**Cách C — Paste đầu chat (one-off):**

```
[Đính kèm core-workflow.md hoặc paste nội dung]

Task: <mô tả việc cần làm>
```

### 1.2 Với Claude Code / Codex CLI / Cline

```bash
# Claude Code
claude --append-system-prompt "$(cat ai-coding-plan/core-workflow.md)"

# Codex CLI
codex --instructions ai-coding-plan/core-workflow.md
```

Hoặc dùng tham số `system_prompt` / `additional_instructions` của agent SDK tương ứng.

### 1.3 Với Cursor SDK / API tự build

```ts
import { Agent } from "@cursor/sdk";
import { readFileSync } from "node:fs";

const systemPrompt = readFileSync("ai-coding-plan/core-workflow.md", "utf8");

await Agent.create({
  systemPrompt,
  // ...
});
```

### 1.4 Khi nào cần update `core-workflow.md`

Update khi:

- Stack đổi (vd. naik lên Laravel 13, MUI v7, đổi từ pnpm sang bun).
- Naming convention thay đổi (annotation `@type:` thêm/bớt).
- Octane primitives thay đổi (`PerRequestFlags`, `OctaneContext`).
- Cấu trúc package đổi (vd. nếu gộp `packages/framework/` và `packages/metafox/`).

---

## 2. Cách dùng `prompt-commands.md`

`prompt-commands.md` là **thư viện 22 chương** chứa các prompt template đã viết sẵn cho từng nhiệm vụ phổ biến. Mỗi prompt có:

- **Mục tiêu** — mô tả việc cần làm.
- **Prompt mẫu** — copy/paste, có sẵn `@file` references để agent đọc context.
- **Files / patterns liên quan** — đường dẫn cụ thể trong codebase.

### 2.1 Workflow điển hình

```
1. Tìm chương phù hợp trong Mục lục (§1–22).
2. Copy prompt mẫu.
3. Thay placeholder `<module>`, `<feature>`, `<resource>` bằng giá trị thật.
4. Paste vào Cursor Composer / Claude Code / Cline.
5. Agent đọc file `@reference` trước, sau đó sinh patch.
6. Review patch theo Validation checklist trong `core-workflow.md` §6.
```

### 2.2 Mục lục nhanh

| § | Domain | Prompt template điển hình |
|---|--------|---------------------------|
| 1 | Tạo block mới | "Tạo block listing extends `core.block.listview` cho resource `<name>`..." |
| 2 | Item view & skeleton | "Tạo `<resource>.itemView.mainCard` + skeleton tương ứng..." |
| 3 | Layout JSON | "Cập nhật `layouts.json` để place block vào slot..." |
| 4 | Refactor layout | "Migrate inline block code sang `createBlock` HOC..." |
| 5 | Responsive variants | "Tạo bản `small` của block `<X>` với layout single-column..." |
| 6 | Page params | "Truyền `module_name`/`item_type` qua `usePageParams`..." |
| 7 | Styling block | "Apply theme palette cho block thay vì hardcode hex..." |
| 8 | Debug | "Block `<name>` không render — trace từ `layouts.json` → `createBlock`..." |
| 9 | Workflow tổng hợp | Multi-step cho feature mới (block + saga + reducer + i18n). |
| 10 | PR review checklist | Audit 1 PR theo MetaFox conventions. |
| 11 | Form Builder | "Tạo form `<feature>` với schema `@metafox/form`..." |
| 12 | Dialogs | "Tạo dialog `<module>.dialog.<purpose>` + present qua `dialogBackend`..." |
| 13 | Routing & Modal routes | "Tạo modal route `<resource>.viewModal`..." |
| 14 | Theme & Styling | "Đăng ký theme mới + override MuiButton..." |
| 15 | Sagas | "Tạo saga `takeLatest` gọi `getResourceAction` + dispatch..." |
| 16 | Services & Manager | "Đăng ký class service vào Manager, augment typings..." |
| 17 | State / Redux | "Tạo paging reducer cho resource `<X>`..." |
| 18 | i18n & Translations | "Thêm phrase key + dùng `i18n.formatMessage` ICU plural..." |
| 19 | Cookies & Local Storage | "Persist UI preference qua `localStore`..." |
| 20 | TypeScript augmentation | "Augment `Manager` / `GlobalState` qua `module.d.ts`..." |
| 21 | When conditions | "Hide menu item bằng `showWhen` rule nested..." |
| 22 | `@type:` cheat sheet | Map đầy đủ 14 annotation. |

### 2.3 Ví dụ cụ thể: tạo feature "blog reactions"

Feature này cần chạm: backend (Eloquent + repo + controller + resource + form request + policy + migration) + frontend (block + saga + reducer + i18n + types). Workflow:

```
Bước 1 — Backend module:
  Mở Cursor Composer, dán prompt từ §11 (form) + §15 (sagas) trong
  ai-coding-plan/prompt-commands.md tinh chỉnh cho resource = "blog_reaction".

Bước 2 — Frontend block:
  §1 (Tạo block) → "Tạo block.listing dùng dataSource /blog/{id}/reaction"
  §2 (itemView) → "Tạo blog_reaction.itemView.mainRow"

Bước 3 — Saga + Reducer:
  §15 → "saga blog_reaction.saga.like" với takeLatest
  §17 → "Paging reducer cho /blog/{id}/reaction"

Bước 4 — i18n:
  §18 → "Phrase keys: blog::reaction.like, blog::reaction.unlike"

Bước 5 — Validate:
  Theo Validation Checklist trong core-workflow.md §6.
```

### 2.4 Khi nào cần update `prompt-commands.md`

- Có thư viện / pattern mới (vd. dùng React Query song song redux-saga).
- Naming convention đổi.
- Annotation `@type:` thêm/bớt → update §22.
- Có file reference mới đáng đọc trước → thêm vào cheat sheet cuối file.

---

## 3. Quick start cho session mới

```
1. Setup 1 lần:
   Cursor User Rules ← paste core-workflow.md
   (hoặc giữ nguyên backend/AGENTS.md & frontend/AGENTS.md)

2. Mỗi task:
   a. Mở prompt-commands.md, tìm chương phù hợp
   b. Copy prompt template
   c. Thay placeholder → paste vào Composer
   d. Agent sinh patch
   e. Review theo Validation Checklist
   f. pnpm run reload (nếu có annotation mới) / php artisan octane:reload (nếu có cache state)
```

## 4. Best practices

- **Đừng** chỉ paste task ngắn (vd. "tạo block blog"). Always đính kèm `@docs/...` references — agent có context tốt hơn 10x.
- **Đừng** paste cả 1146 dòng `prompt-commands.md` — chỉ cần đoạn của 1 chương + `@file` refs.
- **Luôn** confirm output theo §6 Validation Checklist trong `core-workflow.md` trước khi commit.
- **Khi sửa lớn** (refactor, migration): chạy 1 round `core-workflow.md` §6 + thêm `@frontend/.cursor/rules/` hoặc `@backend/.cursor/rules/` để agent áp đúng convention.
- **Khi debug**: prompt nên kèm log/error nguyên văn + breakpoint context, không chỉ "không chạy".

## 5. Update / maintenance

| Khi nào | File cần update |
|---------|-----------------|
| Stack version bump | `core-workflow.md` (Tech stack section) + `frontend/.cursor/rules/always/metafox-stack.mdc` |
| Annotation mới (vd. `@type: webhook`) | `prompt-commands.md` §22 + thêm chương riêng nếu phổ biến |
| Convention naming đổi | `core-workflow.md` (Workflow section) + chương liên quan trong `prompt-commands.md` |
| Doc nguồn (`docs/src/pages/...`) đổi | Chỉ update `prompt-commands.md` nếu prompt còn reference path / pattern cũ |
| PhpFox legacy ref | Chỉ update `legacy/README.md`, không động đến `phpfox-docs.*` |

> Quy tắc: **doc nguồn (`docs/src/pages/`, `.cursor/rules/`) là source of truth** — luôn update bên đó trước, sau đó xem `core-workflow.md` / `prompt-commands.md` còn cần đồng bộ không.

---

## 6. Tham khảo nâng cao

- `ai-coding-plan.md` — lộ trình học AI agent từ Level 1 → 5 + concept (RAG, ReAct, MCP, multi-agent).
- `backend/.cursor/rules/manual/unit-testing-*.mdc` — 11 file rule cho từng loại unit test (controller, repository, policy, observer, ...).
- `frontend/.cursor/rules/sagas-services.mdc` — convention chi tiết saga + service header.
- Live docs: `docs/src/pages/{frontend,backend}/*.mdx` — luôn check trước khi tin doc cũ.

---

*Phiên bản: 5.x (May 2026). Cập nhật khi MetaFox đổi major architecture (Octane → Roadrunner, Laravel 13, MUI v7, ...).*
