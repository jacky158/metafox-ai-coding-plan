# AI Agent Coding — Hướng Dẫn Toàn Diện

> Tài liệu tổng hợp về AI Agent Coding: concepts, roadmap học tập, tích hợp Cursor IDE với Laravel + ReactJS, và các level áp dụng thực tế.

---

## Mục lục

1. [Tổng quan AI Agent Coding](#1-tổng-quan-ai-agent-coding)
2. [Roadmap học tập theo 5 level](#2-roadmap-học-tập-theo-5-level)
3. [Tích hợp Cursor IDE với Laravel + ReactJS](#3-tích-hợp-cursor-ide-với-laravel--reactjs)
4. [Các level áp dụng AI Coding Agent](#4-các-level-áp-dụng-ai-coding-agent)
5. [Lộ trình adopt cho team](#5-lộ-trình-adopt-cho-team)
6. [Resources & tools tổng hợp](#6-resources--tools-tổng-hợp)

---

## 1. Tổng quan AI Agent Coding

AI Agent Coding là việc sử dụng LLM (Large Language Model) không chỉ để gợi ý code mà còn để **tự lên kế hoạch, thực thi nhiều bước, sử dụng tools, và hoàn thành tasks phức tạp** với ít hoặc không cần can thiệp của con người.

### Điểm khác biệt so với AI Autocomplete thông thường

|           | AI Autocomplete (L1)        | AI Agent (L3–L5)                   |
|-----------|-----------------------------|------------------------------------|
| Scope     | Một dòng / một hàm          | Nhiều file, toàn feature           |
| Hành động | Suggest → người dùng accept | Plan → Execute → Verify → Iterate  |
| Tools     | Không có                    | File system, terminal, DB, browser |
| Context   | File đang mở                | Toàn codebase + external tools     |
| Autonomy  | Rất thấp                    | Cao (tùy level)                    |

---

## 2. Roadmap học tập theo 5 level

### Level 1 — Nền tảng: AI & LLM Fundamentals

**Mục tiêu:** Hiểu cách LLM hoạt động trước khi dùng nó làm "não" của agent.

**Concepts cần nắm:**

- **LLM fundamentals:** Token, context window, temperature, top-p, system prompt
- **Prompt engineering:** Zero-shot, few-shot, chain-of-thought, role prompting
- **LLM APIs:** OpenAI, Anthropic (Claude), Google Gemini, Ollama (local)
- **Embeddings & vectors:** Semantic search, cosine similarity, vector databases

**Tech cần biết:** Python hoặc TypeScript, REST API calls, JSON mode, streaming responses

**Thời gian:** 1–2 tuần (nếu đã biết lập trình)

---

### Level 2 — Agent Building Blocks

**Mục tiêu:** Nắm vững các khái niệm cốt lõi tạo nên một AI Agent.

**Concepts cần nắm:**

- **Tool use / Function calling**
  - Định nghĩa tool schema (JSON Schema)
  - Parse và execute tool calls từ LLM response
  - Handle tool results và trả về context

- **ReAct pattern (Reason → Act → Observe)**
  - Agent suy nghĩ (Thought) → hành động (Action) → quan sát kết quả (Observation)
  - Loop cho đến khi task hoàn thành

- **Memory management**
  - In-context memory (trong conversation)
  - External memory (vector DB, database)
  - Episodic memory (lịch sử hành động)
  - Semantic memory (kiến thức tổng quát)

- **RAG (Retrieval-Augmented Generation)**
  - Chunking và embedding documents
  - Vector search để retrieve context liên quan
  - Augment prompt với retrieved context

- **Structured output**
  - Pydantic models, JSON Schema
  - Output parsers, retry on parse error

- **Planning & task decomposition**
  - Task splitting thành subgoals
  - Sequential vs parallel execution

**Tools & frameworks:** LangChain, LlamaIndex, Pydantic, Chroma, Pinecone

**Thời gian:** 2–4 tuần

---

### Level 3 — Agent Patterns & Frameworks

**Mục tiêu:** Hiểu và implement các kiến trúc agent phổ biến.

**Patterns cần nắm:**

- **Single agent autonomous loop**
  - Self-reflection và self-correction
  - Retry mechanisms, error handling
  - Stopping conditions

- **Multi-agent systems**
  - Orchestrator agent điều phối workers
  - Handoff patterns giữa agents
  - Shared memory và communication

- **Code agent**
  - Code generation và execution trong sandbox
  - Debugging loop (chạy → đọc error → sửa → lặp)
  - Repository-level code understanding

- **Browser / Computer use agent**
  - Web scraping và UI automation
  - Vision model để "nhìn" màn hình
  - Action space: click, type, navigate

- **Human-in-the-loop**
  - Approval gates cho hành động quan trọng
  - Clarification khi không chắc chắn
  - Interrupt và resume

**Frameworks phổ biến:**

| Framework         | Điểm mạnh                       | Phù hợp với            |
|-------------------|---------------------------------|------------------------|
| LangGraph         | Graph-based, stateful, flexible | Complex workflows      |
| CrewAI            | Multi-agent, role-based         | Team simulation        |
| AutoGen           | Conversational multi-agent      | Research, prototyping  |
| OpenAI Agents SDK | Lightweight, production-ready   | Simple → medium agents |
| Agno              | Fast, minimal                   | Performance-critical   |

**Thời gian:** 4–6 tuần

---

### Level 4 — Advanced Concepts

**Mục tiêu:** Nâng cao độ tin cậy, hiệu năng và khả năng mở rộng.

**Concepts cần nắm:**

- **MCP (Model Context Protocol)**
  - Giao thức chuẩn hóa để agent kết nối với tools/context
  - MCP server: filesystem, database, GitHub, browser...
  - Tích hợp MCP với Cursor IDE, Claude Code

- **State management & checkpointing**
  - Persistent state cho long-running tasks
  - Resume sau khi bị gián đoạn
  - Rollback khi có lỗi

- **Async & parallel agents**
  - Concurrent task execution (asyncio)
  - Fan-out / fan-in patterns
  - Rate limiting và backoff

- **Evaluation & testing**
  - LLM-as-judge để đánh giá output
  - Trajectory evaluation (đánh giá chuỗi hành động)
  - Regression testing cho agent behavior

- **Guardrails & safety**
  - Input validation (phòng prompt injection)
  - Output validation (kiểm tra trước khi execute)
  - Hallucination detection

- **Observability & tracing**
  - LangSmith, Arize Phoenix, OpenTelemetry
  - Trace từng bước agent decision
  - Cost monitoring

**Tools:** MCP servers, asyncio, LangSmith, Arize Phoenix, Guardrails AI

**Thời gian:** 1–2 tháng

---

### Level 5 — Production & Specialized Domains

**Mục tiêu:** Deploy agent vào môi trường thực tế và xây dựng domain-specific agents.

**Concepts cần nắm:**

- **Deployment patterns**
  - FastAPI + Redis Queue cho async agent execution
  - Serverless (Modal, Fly.io) cho on-demand agents
  - Docker containerization

- **Cost & latency optimization**
  - Prompt caching (giảm đến 90% cost cho repeated context)
  - Model routing (dùng model nhỏ cho tasks đơn giản)
  - Response streaming

- **Specialized agents**
  - Data / SQL agents: Text-to-SQL, data analysis
  - Coding agents: SWE-agent architecture, repo-level understanding
  - Domain-specific: RAG-heavy agents cho knowledge-intensive tasks

- **Fine-tuning cho agent tasks**
  - Task-specific model training
  - Knowledge distillation từ larger models
  - RLHF cho agent behavior

**Thời gian:** 3–6 tháng (ongoing)

---

## 3. Tích hợp Cursor IDE với Laravel + ReactJS

### Kiến trúc tổng thể

```
┌─────────────────────────────────────────────────────────┐
│                     Cursor IDE                           │
│  ┌─────────────────┐    ┌──────────────────────────┐    │
│  │  Composer+Chat  │    │     .cursorrules          │    │
│  │  (multi-file)   │    │  (project conventions)   │    │
│  └─────────────────┘    └──────────────────────────┘    │
│  ┌─────────────────────────────────────────────────┐    │
│  │               MCP Servers                        │    │
│  │  filesystem │ MySQL │ GitHub │ Browser │ Artisan │    │
│  └─────────────────────────────────────────────────┘    │
└────────────────────┬────────────────────────────────────┘
                     │ generates / edits
          ┌──────────┴──────────┐
          │                     │
┌─────────▼──────────┐ ┌────────▼────────────┐
│  Laravel Backend   │ │  ReactJS Frontend   │
│                    │ │                     │
│  Models            │ │  Components         │
│  Controllers       │◄──►  Hooks             │
│  Services          │ │  API Services       │
│  Migrations        │ │  State (Zustand)    │
│  Tests (Pest)      │ │  Tests (Vitest)     │
└────────────────────┘ └─────────────────────┘
```

---

### 3.1 Setup Cursor IDE

#### Bước 1: Cài đặt và cấu hình cơ bản

```
1. Tải Cursor tại cursor.com
2. Settings → Models → chọn claude-sonnet-4-5 (hoặc claude-opus cho tasks phức tạp)
3. Settings → Features → Agent: ON
4. Settings → Features → Auto-run terminal: ON (tùy chọn)
5. Settings → Indexing → Enable codebase indexing: ON
```

#### Bước 2: Index docs Laravel và React

```
Cursor Settings → Docs → Add Doc:
- https://laravel.com/docs/11.x
- https://react.dev/reference/react
- https://tanstack.com/query/latest/docs
- https://ui.shadcn.com/docs
```

#### Bước 3: Tạo `.cursorignore`

```gitignore
# .cursorignore
vendor/
node_modules/
.env
storage/logs/
.git/
dist/
build/
*.lock
bootstrap/cache/
public/storage/
```

---

### 3.2 File `.cursorrules` — Quan trọng nhất

Đây là file quan trọng nhất để AI hiểu đúng convention và kiến trúc project. Đặt ở root project.

```markdown
# Project: [Tên project]
# Stack: Laravel 11 (backend) + ReactJS TypeScript (frontend)

## Backend (Laravel)
- PHP 8.3+, Laravel 11
- Testing: Pest PHP
- API: RESTful JSON API, dùng Laravel API Resources
- Auth: Laravel Sanctum (token-based)
- Database: MySQL 8.0, Eloquent ORM (không dùng raw SQL)
- Queue: Redis + Laravel Horizon
- Cache: Redis

### Architecture Pattern
- Controller → Service → Repository
- Controllers: thin, chỉ handle HTTP request/response
- Services: business logic
- Repositories: database queries
- Form Requests cho validation (không validate trong Controller)

### Naming Conventions
- Database: snake_case
- API responses: camelCase
- Classes: PascalCase
- Methods/variables: camelCase

### Testing
- Luôn viết Pest Feature tests cho mỗi API endpoint mới
- Dùng RefreshDatabase trait
- Factories cho test data

### File Structure
app/
├── Http/
│   ├── Controllers/Api/
│   ├── Requests/
│   └── Resources/
├── Services/
├── Repositories/
└── Models/

## Frontend (React)
- React 18 + TypeScript (strict mode)
- Build tool: Vite
- UI: shadcn/ui + Tailwind CSS
- Server state: TanStack Query (React Query v5)
- Global state: Zustand
- HTTP: Axios với interceptors cho auth token
- Form: React Hook Form + Zod validation
- Testing: Vitest + React Testing Library

### Architecture Rules
- Functional components only (không dùng class components)
- Custom hooks để tách business logic khỏi UI
- Feature-based folder structure (không theo type)
- Tất cả API types phải match Laravel API Resource

### File Structure
src/
├── features/
│   └── [feature-name]/
│       ├── components/
│       ├── hooks/
│       ├── services/
│       └── types.ts
├── components/ui/  (shadcn/ui)
├── stores/         (Zustand)
└── lib/            (axios instance, utils)

## Git Conventions
- Branch: feature/[ticket-id]-[short-desc]
- Commit: conventional commits (feat:, fix:, chore:, refactor:)
- PR: squash merge

## Tuyệt đối không
- Không dùng raw SQL, luôn dùng Eloquent
- Không để business logic trong Controller
- Không hardcode strings, dùng constants/enums
- Không class components trong React
- Không any type trong TypeScript
```

> **Lưu ý:** Nếu project có backend và frontend tách thư mục riêng, tạo hai file `.cursorrules` — một ở `/backend`, một ở `/frontend`.

---

### 3.3 MCP Server Setup

Tạo file `~/.cursor/mcp.json` (global) hoặc `.cursor/mcp.json` (per-project):

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/path/to/your/project"
      ]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_your_token_here"
      }
    },
    "mysql": {
      "command": "npx",
      "args": ["-y", "mcp-server-mysql"],
      "env": {
        "MYSQL_HOST": "127.0.0.1",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "root",
        "MYSQL_PASS": "your_password",
        "MYSQL_DB": "your_database_name"
      }
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp"]
    }
  }
}
```

> ⚠️ **Bảo mật:** Không commit file `mcp.json` có credentials lên git. Thêm vào `.gitignore`.

#### Custom Artisan MCP Server (nâng cao)

Cho phép Cursor agent chạy `php artisan` commands trực tiếp:

```javascript
// artisan-mcp/index.js
import { execSync } from 'child_process'
import { Server } from '@modelcontextprotocol/sdk/server/index.js'
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js'

const server = new Server(
  { name: 'artisan-mcp', version: '1.0.0' },
  { capabilities: { tools: {} } }
)

server.setRequestHandler('tools/list', async () => ({
  tools: [{
    name: 'artisan',
    description: 'Run Laravel Artisan commands',
    inputSchema: {
      type: 'object',
      properties: {
        command: { type: 'string', description: 'Artisan command to run' }
      },
      required: ['command']
    }
  }]
}))

server.setRequestHandler('tools/call', async ({ params }) => {
  if (params.name === 'artisan') {
    const output = execSync(`php artisan ${params.arguments.command}`, {
      cwd: '/path/to/laravel/project'
    }).toString()
    return { content: [{ type: 'text', text: output }] }
  }
})

const transport = new StdioServerTransport()
await server.connect(transport)
```

---

### 3.4 Workflow thực chiến — Laravel

#### Generate toàn bộ feature từ một prompt

```
@Codebase Tạo feature quản lý Products cho API.

Yêu cầu:
- Migration: products table
  (name, slug, price, stock, category_id, is_active, timestamps)
- Model Product với:
  - Relationship: belongsTo Category
  - Scopes: active(), inStock()
  - Casts: price → decimal, is_active → boolean
- ProductRepository với: findAll(filters), findById, create, update, softDelete
- ProductService xử lý business logic (validate stock, generate slug)
- ProductController (API Resource controller, 5 methods chuẩn)
- StoreProductRequest và UpdateProductRequest
- ProductResource và ProductCollection
- Route trong api.php với middleware auth:sanctum
- Pest Feature tests cho tất cả 5 endpoints
- Đặt file đúng theo cấu trúc đã có trong project
```

#### Debug API với DB context (yêu cầu MySQL MCP)

```
API GET /api/products đang trả về empty array dù DB có data.

Hãy:
1. Dùng MySQL MCP query schema của bảng products
2. Đọc ProductRepository và tìm query có vấn đề
3. Kiểm tra xem có soft delete filter không đúng không
4. Sửa bug và chạy lại test để verify
```

#### Generate tests từ controller có sẵn

```
@ProductController.php @ProductRepository.php

Viết đầy đủ Pest Feature tests cho ProductController.
Cần cover:
- Happy path cho cả 5 endpoints
- Validation errors (400)
- Unauthorized (401)
- Not found (404)
- Edge cases: tạo product với slug đã tồn tại

Dùng ProductFactory đã có, RefreshDatabase trait.
```

---

### 3.5 Workflow thực chiến — ReactJS

#### Generate React feature từ Laravel API response

```
@Codebase Laravel API /api/products trả về:
{
  data: [{
    id: number,
    name: string,
    slug: string,
    price: number,
    stock: number,
    category: { id: number, name: string },
    isActive: boolean,
    createdAt: string
  }],
  meta: { currentPage, lastPage, total, perPage }
}

Tạo feature Products:
1. TypeScript interfaces: Product, Category, ProductsResponse
2. productService.ts: CRUD functions dùng axios instance
3. useProducts(filters) hook với React Query (list + pagination)
4. useProduct(id) hook cho detail
5. useProductMutations() hook cho create/update/delete
6. ProductsPage: table với columns, filter bar, pagination
7. ProductFormDialog: create và edit trong Dialog (shadcn/ui)
8. Kết nối authStore để lấy token từ Zustand
```

#### Generate component từ screenshot (vision)

```
1. Chụp màn hình hoặc copy design từ Figma
2. Paste vào Cursor Chat (Cmd+V)
3. Prompt:

"Tạo React component từ design này.
Tên component: ProductCard
Props: product: Product (từ types đã có trong @src/features/products/types.ts)
Dùng shadcn/ui Card, Badge
Dùng Tailwind CSS theo design system hiện tại
Responsive: mobile-first"
```

#### E2E test với Playwright MCP

```
@ProductsPage.tsx Viết Playwright test cho trang Products:

1. Test render: bảng hiển thị đúng columns
2. Test filter: nhập search → list update
3. Test create: mở dialog → điền form → submit → toast success
4. Test pagination: click next page → URL update
5. Test delete: click delete → confirm dialog → toast success

Dùng test fixtures có sẵn trong /tests/fixtures/
```

---

## 4. Các level áp dụng AI Coding Agent

### Level 1 — AI Autocomplete: "Tab để code"

**Mô tả:** Dùng AI như một autocomplete thông minh. Viết phần đầu, AI gợi ý phần còn lại.

**Tools:** GitHub Copilot, Cursor Tab, Supermaven

**Làm được:**
- Gợi ý tự động hoàn thành dòng code theo context
- Suggest function body khi thấy signature
- Ghost text preview trước khi accept
- Học pattern từ codebase hiện tại

**Giới hạn:**
- Chỉ nhìn thấy file đang mở
- Không hiểu business context
- Vẫn phải tự plan và navigate

**Tăng năng suất:** ~+20% | **Thời gian adopt:** 1–2 ngày

```php
// Laravel: viết tên method, AI tự hoàn thành body
public function getUsersByRole(string $role): Collection
// → AI suggest: return User::where('role', $role)->active()->get();
```

```typescript
// React: gõ use, AI suggest hook phù hợp với file context
const [products, setProducts] = use// → useProducts()
```

---

### Level 2 — AI Chat trong IDE: "Hỏi để hiểu & sửa"

**Mô tả:** Chat với AI về code đang mở. Giải thích, refactor, debug từng đoạn code cụ thể.

**Tools:** Cursor Chat, Copilot Chat, `@file` context

**Làm được:**
- Explain code phức tạp bằng ngôn ngữ tự nhiên
- Refactor function, cải thiện readability
- Debug: paste error message → AI phân tích root cause
- Convert: SQL → Eloquent, JavaScript → TypeScript, REST → GraphQL
- Generate unit test cho function đang chọn
- Review code và suggest improvements

**Giới hạn:**
- Chỉ làm việc với đoạn code được share trong chat
- Không tự apply vào file, phải copy thủ công
- Không nhớ context giữa các cuộc hội thoại

**Tăng năng suất:** ~+40% | **Thời gian adopt:** 1 tuần

```
# Prompt mẫu hiệu quả

@ProductController.php Hàm index() đang bị N+1 query.
Explain lý do tại sao và refactor lại dùng eager loading.
Giữ nguyên API response format.

---

@useProducts.ts Convert hook này sang dùng React Query v5.
Giữ nguyên interface, chỉ thay implementation.
Đảm bảo error handling và loading states.
```

---

### Level 3 — Multi-file Composer: "Describe để generate"

**Mô tả:** AI tự tạo, sửa nhiều file cùng lúc theo mô tả. **Đây là bước nhảy vọt về năng suất.**

**Tools:** Cursor Composer, `@Codebase`, `.cursorrules`, Claude Sonnet/Opus

**Làm được:**
- Generate toàn bộ feature (migration → model → controller → test) từ một prompt
- Refactor cross-file (rename, restructure, thay đổi architecture)
- Sync interface giữa Laravel API Resource và React TypeScript types
- Apply convention từ `.cursorrules` tự động và nhất quán
- Tạo code theo đúng pattern của project hiện tại

**Giới hạn:**
- AI không thể chạy code để verify kết quả
- Context window có giới hạn (~200k tokens với Claude)
- Cần review kỹ trước khi commit

**Tăng năng suất:** ~+200% | **Thời gian adopt:** 2–3 tuần

> ⚡ **Key insight:** L3 là level quan trọng nhất để adopt. Barrier chính là `.cursorrules` — nếu file này không đủ tốt, AI sẽ code sai convention và bạn mất tin tưởng vào tool. Dành 2–3 giờ viết `.cursorrules` thật kỹ ngay từ đầu.

---

### Level 4 — Agentic Loop: "Delegate để agent tự xử lý"

**Mô tả:** Agent tự lên kế hoạch, chạy terminal, đọc kết quả, sửa lỗi và lặp lại — không cần can thiệp.

**Tools:** Cursor Agent mode + MCP servers, Claude Code CLI

**Làm được:**
- Chạy `php artisan migrate`, `npm run build` tự động
- Đọc test failures → tự debug → chạy lại tests → iterate
- Query database thật qua MCP để verify data
- Mở browser qua Playwright MCP, kiểm tra UI render, sửa CSS
- Tự tìm file cần sửa mà không cần chỉ định
- Git: tạo branch, commit có nghĩa, tạo PR tự động

**Cần chú ý:**
- Setup MCP servers đúng cách trước khi dùng
- Đặt review checkpoint trước khi agent commit lên git
- Agent có thể tốn nhiều tokens hơn (monitor cost)
- Không cho agent access production database

**Tăng năng suất:** ~+400% | **Thời gian adopt:** 1–2 tháng

```
# Prompt mẫu — agentic debug loop

API /api/orders đang fail với lỗi 500 trên staging.
Hãy:
1. Đọc Laravel logs trong storage/logs/ tìm stack trace
2. Tìm file liên quan và identify root cause
3. Fix bug
4. Chạy php artisan test --filter=OrderTest để verify
5. Nếu tất cả tests pass, tạo commit "fix: resolve order 500 error"
6. Tóm tắt: nguyên nhân là gì và đã sửa như thế nào
```

---

### Level 5 — Autonomous Agent Pipeline: "Ship feature tự động"

**Mô tả:** Agent nhận ticket từ Jira/Linear, tự implement → test → tạo PR. Con người chỉ review và approve.

**Tools:** Claude Code, custom agent pipelines, CI/CD integration, multi-agent systems

**Làm được:**
- Đọc ticket Jira/Linear → phân tích acceptance criteria
- Tạo branch, implement full feature (backend + frontend)
- Chạy toàn bộ test suite, tự fix failures
- Tạo PR với description đầy đủ, screenshots, test coverage
- Multi-agent: orchestrator điều phối backend agent + frontend agent song song
- Tự update ticket status sau khi hoàn thành

**Yêu cầu để áp dụng:**
- Test coverage ≥ 70% (agent cần tests để verify work)
- Codebase stable và có clear conventions
- Team đã quen review AI-generated code (L3–L4 trước)
- CI/CD pipeline đủ robust
- Human review gate bắt buộc trước khi merge

**Tăng năng suất:** 10x+ | **Thời gian adopt:** 3–6 tháng

```
# Pipeline ví dụ (trigger từ webhook khi ticket → "In Progress")

Trigger: PROJ-142 chuyển sang "In Progress"
Title: "Add product export to CSV feature"
Acceptance criteria:
  - User có thể download products list as CSV
  - CSV bao gồm: name, price, stock, category, status
  - Admin only (middleware check)

Agent tự động:
1. Tạo branch: feature/PROJ-142-product-csv-export
2. Backend: ExportController, CsvExportService, route, Pest test
3. Frontend: ExportButton component, useExport hook
4. Chạy full test suite
5. Tạo PR với description đầy đủ
6. Update ticket PROJ-142 → "In Review"
7. Assign PR reviewer theo CODEOWNERS
```

---

## 5. Lộ trình adopt cho team

### Phân tích vị trí hiện tại

```
Câu hỏi tự đánh giá:

□ Team có đang dùng AI autocomplete không? → L1
□ Team có chat với AI để debug/explain không? → L2  
□ Team có dùng Composer để generate feature không? → L3
□ Team có setup MCP và để agent tự chạy terminal không? → L4
□ Team có autonomous pipeline từ ticket đến PR không? → L5
```

### Roadmap 6 tháng cho Laravel + React team

#### Tháng 1 — Toàn team lên L1 → L2

**Actions:**
- Cài Cursor cho tất cả team members
- Workshop 1 buổi (2–3h): demo Chat, `@file`, refactor, debug workflow
- Thực hành: mỗi dev dùng AI Chat cho ít nhất 3 tasks trong tuần đầu
- Đo metric baseline: thời gian debug trung bình, time-to-PR

**Expected outcome:** Toàn team quen với AI-assisted debugging và code explanation.

#### Tháng 2 — Viết `.cursorrules` chuẩn + lên L3

**Actions:**
- Tech lead dành 2–3h viết `.cursorrules` phản ánh đúng kiến trúc project
- Workshop: demo Composer tạo feature end-to-end (migration → model → controller → test → React feature)
- Team thực hành: mỗi feature mới phải thử qua Composer trước
- Retrospective cuối tháng: AI code có đúng convention chưa? Cần update `.cursorrules` gì?

**Expected outcome:** Team tạo feature nhanh hơn 2–3x, code AI generate match đúng pattern.

#### Tháng 3 — Setup MCP + thử L4

**Actions:**
- 1 dev (hoặc tech lead) setup MySQL MCP + GitHub MCP + Filesystem MCP
- Document lại config và chia sẻ với team
- Thử workflow: agent đọc DB schema → generate migration → chạy test → commit
- Nếu tốt: roll out cho toàn team và viết internal guide

**Expected outcome:** Agent có thể tự debug database issues và tạo code dựa trên actual schema.

#### Tháng 4–6 — Đánh giá và cân nhắc L5

**Điều kiện để tiến lên L5:**
- Test coverage ≥ 70%
- Codebase stable (ít legacy debt)
- Team thoải mái review AI-generated code
- Có bandwidth để build automation infrastructure

**Nếu chưa đủ điều kiện:** Tối ưu L3–L4, viết thêm tests, refactor legacy code với sự hỗ trợ của AI.

---

### Những sai lầm phổ biến khi adopt

| Sai lầm                              | Hậu quả                                          | Cách tránh                                     |
|--------------------------------------|--------------------------------------------------|------------------------------------------------|
| Không viết `.cursorrules`            | AI code sai convention → mất tin tưởng           | Viết `.cursorrules` kỹ trước khi dùng Composer |
| Dùng L5 khi chưa có tests            | Agent ship code broken → rollback → mất niềm tin | Đảm bảo test coverage ≥ 70% trước              |
| Commit ngay không review             | Bug lọt vào production                           | Luôn review AI-generated code trước khi commit |
| Setup MCP với production credentials | Security risk                                    | Chỉ dùng dev/staging DB cho MCP                |
| Dừng lại ở L2 vì ngại thay đổi       | Bỏ lỡ 80% giá trị                                | Ép bản thân thử Composer cho 1 feature thật    |

---

## 6. Resources & Tools tổng hợp

### Tools theo category

#### AI Coding IDEs & Extensions

| Tool               | Type      | Điểm mạnh                         |
|--------------------|-----------|-----------------------------------|
| Cursor             | IDE       | Composer, Agent mode, MCP support |
| GitHub Copilot     | Extension | Integration với VS Code/JetBrains |
| Claude Code        | CLI       | Terminal-first, autonomous coding |
| Windsurf (Codeium) | IDE       | Fast autocomplete, Cascade agent  |

#### Agent Frameworks

| Framework         | Language  | Use case                    |
|-------------------|-----------|-----------------------------|
| LangGraph         | Python    | Complex stateful workflows  |
| CrewAI            | Python    | Multi-agent collaboration   |
| AutoGen           | Python    | Conversational multi-agent  |
| OpenAI Agents SDK | Python/JS | Simple production agents    |
| Agno              | Python    | Performance-critical agents |

#### MCP Servers hữu ích

| MCP Server                                | Install  | Dùng cho                       |
|-------------------------------------------|----------|--------------------------------|
| `@modelcontextprotocol/server-filesystem` | `npx -y` | Read/write project files       |
| `@modelcontextprotocol/server-github`     | `npx -y` | GitHub PR, issues, commits     |
| `mcp-server-mysql`                        | `npx -y` | MySQL database queries         |
| `mcp-server-postgres`                     | `npx -y` | PostgreSQL database queries    |
| `@playwright/mcp`                         | `npx -y` | Browser automation, UI testing |
| `@modelcontextprotocol/server-slack`      | `npx -y` | Slack messages, channels       |

#### Vector Databases (cho RAG)

| Tool     | Điểm mạnh                 | Pricing   |
|----------|---------------------------|-----------|
| Chroma   | Open source, local        | Free      |
| Pinecone | Managed, production-ready | Freemium  |
| Weaviate | Open source, self-host    | Free/paid |
| pgvector | PostgreSQL extension      | Free      |

#### Observability & Evaluation

| Tool          | Dùng cho                             |
|---------------|--------------------------------------|
| LangSmith     | Trace LLM calls, evaluate agent runs |
| Arize Phoenix | Open source LLM observability        |
| Helicone      | LLM proxy, cost monitoring           |
| Braintrust    | Eval framework                       |

---

### Prompt templates cho Laravel + React

#### Template 1: New Feature (full-stack)

```
@Codebase Tạo feature [FEATURE_NAME]:

Backend (Laravel):
- Migration: [table_name] ([columns])
- Model [ModelName] với relationships: [relationships]
- [ModelName]Repository: findAll, findById, create, update, delete
- [ModelName]Service: [business logic cần thiết]
- [ModelName]Controller (API Resource)
- Store[ModelName]Request, Update[ModelName]Request
- [ModelName]Resource
- Routes trong api.php (auth:sanctum middleware)
- Pest Feature tests cho tất cả endpoints

Frontend (React):
- TypeScript interface [ModelName]
- [feature]Service.ts (API calls)
- use[Feature]s hook (list + pagination)
- use[Feature](id) hook (detail)
- use[Feature]Mutations hook (CUD)
- [Feature]Page component
- [Feature]FormDialog component (create + edit)

Follow đúng architecture pattern của project.
```

#### Template 2: Debug (với logs)

```
Có lỗi [ERROR_TYPE] ở [ENDPOINT/COMPONENT].

Symptoms:
- [Mô tả triệu chứng]
- [Expected behavior]
- [Actual behavior]

Hãy:
1. Đọc file log liên quan (nếu có MCP)
2. Tìm root cause
3. Propose fix với explanation
4. Implement fix
5. Thêm test case để prevent regression
```

#### Template 3: Refactor

```
@[file.php] Refactor function [functionName]:

Issues hiện tại:
- [Issue 1: e.g., N+1 query]
- [Issue 2: e.g., quá dài, cần extract]

Yêu cầu sau refactor:
- Giữ nguyên method signature và return type
- Giữ nguyên behavior (không breaking change)
- Viết/update unit tests
- Giải thích thay đổi
```

---

### Checklist trước khi ship AI-generated code

```
Code review checklist:

□ Logic có đúng với requirements không?
□ Convention match với .cursorrules không?
□ Không có hardcoded credentials, API keys
□ Error handling đầy đủ (try-catch, validation)
□ N+1 query issues (Laravel: eager loading đúng chưa?)
□ TypeScript types đúng (không có `any`)
□ Tests cover happy path và edge cases
□ Migration có rollback (down method) không?
□ API response format nhất quán với các endpoints khác
□ Không có unused imports, dead code
```

---

*Tài liệu được tổng hợp từ conversation về AI Agent Coding, tích hợp Cursor IDE với Laravel + ReactJS stack.*

*Cập nhật: Tháng 5/2026*