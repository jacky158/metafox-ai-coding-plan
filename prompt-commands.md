# Prompt Commands — Frontend

Tài liệu này liệt kê **những việc developer có thể nhờ AI agent làm** khi làm việc với MetaFox frontend (React 18 + TypeScript + MUI v6 + Redux + redux-saga + workspace `@metafox/*`). Mỗi mục có:

- **Mục tiêu**: việc cần làm.
- **Prompt mẫu**: copy/paste vào Cursor Composer, Claude Code, Cline, v.v.
- **Files / patterns liên quan**: nơi AI nên đọc trước khi sinh code.

> Tham chiếu chính: `docs/src/pages/frontend/*.mdx`, `docs/HOW_TO_CREATE_LAYOUT_BLOCK.md`, các package trong `frontend/packages/framework/`.

## Mục lục

**Layouts & Blocks**

1. Tạo block mới
2. Item view & skeleton
3. Đăng ký / sửa layout JSON
4. Sửa / refactor layout có sẵn
5. Responsive variants
6. Page params & data flow
7. Styling block / theme
8. Debug & troubleshooting
9. Workflow tổng hợp (multi-step)
10. Checklist PR review

**Frontend khác**

11. Form Builder (JSON schema + json2yup)
12. Dialogs (`dialogBackend`, `useDialog`)
13. Routing & Modal routes
14. Theme & Styling (`@type: theme`, dark mode, MUI styled)
15. Sagas (redux-saga + `@metafox/framework`)
16. Services & Manager (`@type: service`)
17. State / Redux (reducers, entities, paging)
18. i18n & Translations (phrase keys, `i18n.formatMessage`)
19. Cookies & Local Storage (`cookieBackend`, `localStore`)
20. TypeScript augmentation (`module.d.ts`)
21. When conditions (`showWhen` / `privacyWhen`)
22. Annotation cheat sheet (`@type:` map)

---

## 1. Tạo block mới (most common)

### 1.1 Block listing kiểu chuẩn — extend `core.block.listview`

**Mục tiêu:** thêm 1 block listing cho resource X (blog, photo, video, custom...).

**Prompt:**

```
@HOW_TO_CREATE_LAYOUT_BLOCK.md @frontend/packages/metafox/blog/src/blocks/BrowseBlogs/Block.tsx

Tạo block listing mới cho resource "<resource>" tại
`frontend/packages/metafox/<module>/src/blocks/<PascalCaseName>/Block.tsx`.

Yêu cầu:
- JSDoc header `@type: block`, name `<module>.block.<Name>`, title, keywords, description.
- Dùng `createBlock<ListViewBlockProps>` extend `core.block.listview`.
- `overrides`: contentType `<resource>`, dataSource (apiUrl, apiParams, pagingId).
- `defaults`: title, itemView `<resource>.itemView.mainCard`, blockLayout, gridLayout.
- Match phong cách neighbor blocks trong cùng package.
```

### 1.2 Block sidebar dạng category — extend `core.categoryBlock`

**Prompt:**

```
@docs/HOW_TO_CREATE_LAYOUT_BLOCK.md

Tạo SideCategoryBlock cho module `<module>`:
- File: `frontend/packages/metafox/<module>/src/blocks/SideCategory/Block.tsx`
- extendBlock `core.categoryBlock`
- defaults: title `Categories`, appName `<module>`
- Header @type: block, name `<module>.block.sideCategory`, keywords `<module>, category`.
```

### 1.3 Bespoke block (custom UI, không extend)

**Mục tiêu:** widget riêng (banner, stat card, featured authors, ...).

**Prompt:**

```
@docs/HOW_TO_CREATE_LAYOUT_BLOCK.md @frontend/packages/metafox/saved/src/blocks/SidebarTypeFilter/Block.tsx

Tạo bespoke block `<module>.block.<name>`:
- Dùng layout primitives từ `@metafox/layout`: Block, BlockHeader, BlockTitle,
  BlockContent, BlockFooter, HeaderActions, NoContent.
- Lấy data qua `useGlobal()` (apiClient / useGetItems / useSession).
- i18n qua `i18n.formatMessage`, KHÔNG hard-code chuỗi tiếng Anh.
- testid prefix `block <name>` để E2E test target được.
- Nếu cần loggedIn: dùng hook `useLoggedIn` và return null khi false.
```

### 1.4 Detail-view block (trang detail của 1 entity)

**Prompt:**

```
@frontend/packages/metafox/blog/src/blocks/ViewBlog/Block.tsx

Tạo detail-view block cho `<resource>`:
- File: `frontend/packages/metafox/<module>/src/blocks/View<Resource>/Block.tsx`
- Đọc identity từ `usePageParams()`, fetch via `useGetItem`.
- Tận dụng `BlockContext.Provider` cho descendants.
- Honor `authRequired`, `showWhen`, `privacyWhen`.
```

---

## 2. Tạo item view & skeleton đi kèm

### 2.1 Item view mới

**Prompt:**

```
@docs/HOW_TO_CREATE_LAYOUT_BLOCK.md

Tạo item view `<resource>.itemView.<variant>`:
- File: `frontend/packages/metafox/<module>/src/itemViews/<Resource><Variant>/index.tsx`
- Header `@type: itemView`, chunkName `<module>`.
- Props: ItemViewProps<<Resource>ItemShape>.
- Dùng layout primitives ItemView / ItemMedia / ItemText / ItemTitle / ItemSummary.
- Click handler dispatch `@dispatch/<resource>/view` qua `useDispatch`.
- Hỗ trợ variants `mainCard | smallFlat | flatCard` theo project convention.
```

### 2.2 Skeleton cho item view

**Prompt:**

```
@frontend/packages/metafox/blog/src/itemViews/BlogMainCardSkeleton.tsx

Tạo skeleton `<resource>.itemView.<variant>.skeleton`:
- Header `@type: skeleton`, name khớp pattern.
- Layout/aspect ratio tương đương item view thật để tránh layout shift.
- Dùng `ImageSkeleton`, `Skeleton` của `@metafox/ui`.
```

---

## 3. Đăng ký / sửa layout JSON

### 3.1 Thêm block vào trang có sẵn

**Prompt:**

```
Mở `frontend/packages/metafox/<module>/src/assets/pages/<page-name>.json`.

Thêm block sau vào `large.blocks` (slot `<slot>`, sau block id `<refKey>`):
{
  "component": "<module>.block.<name>",
  "slotName": "<main|side|subside|top|bottom>",
  "title": "<Title>",
  "key": "<random-5-char>",
  "blockId": "<random-5-char>",
  "blockStyle": "<Contained|Default|...>",
  "blockLayout": "<Side Contained|Main Listings|...>",
  "gridLayout": "<grid layout name>",
  "dataSource": { "apiUrl": "...", "apiParams": "...", "pagingId": "..." },
  "canLoadMore": true
}

Đảm bảo `key` và `blockId` không trùng với block đã có trong file.
Cập nhật `small.blocks` tương ứng nếu cần variant cho mobile.
```

### 3.2 Tạo trang layout mới (new page)

**Prompt:**

```
@frontend/packages/metafox/core/src/assets/pages/home.member.json
@docs/HOW_TO_CREATE_LAYOUT_BLOCK.md

Tạo `frontend/packages/metafox/<module>/src/assets/pages/<page-name>.json`:
- info.bundle = "web", info.name = "<page-name>"
- templateName "<three-column-fixed | two-column | single-column>"
- Thêm các blocks đã được đăng ký (tên qua `@type: block`).
- Hỗ trợ ít nhất 2 size: `large` (desktop), `small` (mobile).
- Slots phải khớp với template chọn.
```

### 3.3 Wire layout vào route

**Prompt:**

```
@frontend/packages/metafox/<module>/src/pages/<PageName>.tsx

Tạo route component:
- Header `@type: route`, name `<module>.<page-name>`, path `<path>`.
- Render `<Page pageName="<page-name>" pageParams={...}/>` từ `@metafox/layout`.
- pageParams build qua `createPageParams(props, prev => ({ module_name, item_type }))`.
- Nếu trang khác cho member vs visitor: dùng `useLoggedIn` switch giữa 2 pageName.
```

---

## 4. Sửa / refactor layout có sẵn

### 4.1 Thay đổi danh sách block một trang

**Prompt:**

```
Trong `<path/to/page.json>`:
1. Xóa block có blockId `<id>` ở slot `<slot>`.
2. Thay block `<old.name>` bằng `<new.name>`, giữ nguyên blockId/key để không reset config admin.
3. Move block `<id>` từ slot `main` sang slot `side`.
4. Đổi blockLayout của block `<id>` sang `<Side Contained>`.

Verify file vẫn parse JSON valid và mọi block còn được register.
```

### 4.2 Đổi tên / namespace block

**Prompt:**

```
Đổi tên block `<old.name>` → `<new.name>` xuyên codebase:
1. Đổi `name` trong JSDoc header `Block.tsx`.
2. Update `extendBlock` ở các block đang inherit (nếu có).
3. Replace `"component": "<old.name>"` trong tất cả `assets/pages/*.json`.
4. Grep test fixtures / E2E tests có hardcode tên block không.
5. Cập nhật `docs/HOW_TO_CREATE_LAYOUT_BLOCK.md` nếu mention.
```

### 4.3 Migrate từ bespoke block sang `createBlock(...)` extend pattern

**Prompt:**

```
@frontend/packages/metafox/<module>/src/blocks/<Name>/Block.tsx
@frontend/packages/framework/metafox-core/src/hocs/createBlock.tsx

Refactor block thành `createBlock<ListViewBlockProps>({extendBlock, overrides, defaults})`:
- Identify props nào nên là overrides (admin không sửa được) vs defaults (sửa được).
- Giữ nguyên hành vi runtime (dataSource, itemView, authRequired).
- Xóa code duplicate đã được createBlock cung cấp (BlockContext.Provider, testid auto, showWhen/privacyWhen handling).
- Update tests nếu có.
```

---

## 5. Responsive variants

### 5.1 Thêm variant cho size mới

**Prompt:**

```
Trang `<page-name>.json` đang chỉ có `large`. Thêm variant `small` (mobile):
- Template phù hợp mobile (single-column).
- Giữ block thiết yếu (status composer, feed, primary menu).
- Bỏ blocks không quan trọng trên mobile (sidebar widgets).
- Map slotName mới khớp template mobile.

Tham khảo bảng fallback trong @docs/HOW_TO_CREATE_LAYOUT_BLOCK.md §9.
```

### 5.2 Debug khi block không hiển thị ở 1 breakpoint

**Prompt:**

```
Trang `<page>` không show block `<id>` ở size `small`.

Hãy kiểm:
1. Block có trong `small.blocks` không (do variant resolve once at first render — không tự fallback nếu thiếu key)?
2. slotName có khớp template của `small` không?
3. `showWhen` / `privacyWhen` rule có loại trừ ở mobile không?
4. `authRequired` + `useLoggedIn` state mock đúng chưa?

Đề xuất fix và update file json.
```

---

## 6. Page params & data flow

### 6.1 Truyền context xuống block

**Prompt:**

```
Block `<name>` cần biết `parent_module_name` + `parent_item_id` (đang chạy trong group page).

1. Update `<page-name>.json` truyền pageParams chứa các key này (qua route component).
2. Trong block: `const { parent_module_name, parent_item_id } = useParams();`
3. Pass vào dataSource.apiParams nếu cần filter theo group.
4. Add fallback khi block render ở context khác (params undefined).
```

### 6.2 Đồng bộ pageParams khi điều hướng SPA

**Prompt:**

```
Khi user navigate giữa 2 group page, block listing reuse cũ data sai parent_item_id.

Hãy:
1. Verify `createPageParams` đang trả prev callback đúng chưa.
2. Đảm bảo `pagingId` chứa parent_item_id để cache key khác nhau.
3. Test: chuyển group A → B → block phải refetch.
```

---

## 7. Styling / theme

### 7.1 Tạo block style variant

**Prompt:**

```
Block `<name>` cần variant visual mới (e.g. `Compact`).

1. Trong block component, gói outer `<Block blockStyle={blockStyle}>`.
2. Khai báo style trong styles.ts dùng MUI `styled()` hoặc `sx`.
3. Update `<page>.json` block dùng `"blockStyle": "Compact"`.
4. Tài liệu hóa variant trong block JSDoc nếu là public extension surface.
```

### 7.2 Override theme cho 1 page

**Prompt:**

```
Trang `<page>` cần dark variant ngay cả khi user đang light theme.

Suggest cách: wrap `<Page>` bằng `<ChildTheme variant="dark">` từ
`@metafox/layout/ChildTheme`. Verify `BlockHeader` / `BlockContent`
đọc theme đúng và không bị MUI Inherit lỗi.
```

---

## 8. Debug & troubleshooting

### 8.1 Block không xuất hiện trong AdminCP "Add Block" picker

**Prompt:**

```
Tôi vừa add `Block.tsx` mới với `@type: block` nhưng không thấy trong layout editor.

Hãy diagnose:
1. JSDoc header đặt ở dòng đầu file chưa?
2. `name` có duplicate với block khác không (grep `name: <module>.block.`).
3. Đã chạy `pnpm run reload` chưa (regenerate bundle scanner output)?
4. File bundle-* có entry mới chưa? (`grep -r "<module>.block.<name>" frontend/app/src/bundle-*`)
5. Build có warning về annotation parse error không?

Fix và confirm bằng `pnpm run reload && pnpm run start`.
```

### 8.2 Block render lỗi `Cannot read property of undefined`

**Prompt:**

```
@<path-to-block>/Block.tsx

Block crash khi item undefined. Add guard:
- Early return null khi data chưa load (`if (!item) return <SkeletonComponent />`).
- Dùng optional chaining cho nested fields.
- Verify dataSource trả format khớp ItemShape (so với JsonResource backend).
```

### 8.3 Layout fallback không như mong đợi

**Prompt:**

```
Trang `<page>` ở viewport `medium` đang render `xlarge` config thay vì `large`.

Reference: bảng fallback trong @docs/HOW_TO_CREATE_LAYOUT_BLOCK.md §9.
Variant resolve once at first render — kiểm xem `<page>.json` có thiếu
key `medium`/`large` không, hoặc resolver có cache stale không.
```

---

## 9. Workflow tổng hợp (multi-step prompts)

### 9.1 End-to-end: tạo feature listing mới

**Prompt:**

```
@docs/HOW_TO_CREATE_LAYOUT_BLOCK.md

Implement feature browse cho resource `<resource>` từ A → Z:

1. Backend đã có endpoint GET /api/v1/<resource>?view=latest&page=N.
2. Frontend:
   a. Tạo TS types `<Resource>ItemShape` trong `packages/metafox/<module>/src/types.ts`.
   b. Tạo item view `<resource>.itemView.mainCard` + skeleton.
   c. Tạo block `<module>.block.Browse<Resources>` extend `core.block.listview`.
   d. Tạo page json `<resource>.browse.json` (large + small).
   e. Tạo route `<module>.<resource>.browse` (`@type: route`) render `<Page pageName=...>`.
   f. Cập nhật messages.json với phrase keys mới.
3. `pnpm run reload && pnpm run start` — verify trang load, paginate, filter ok.
```

### 9.2 Audit toàn bộ layouts khi đổi convention

**Prompt:**

```
Tôi muốn đổi convention slot name: `subside` → `aside-secondary`.

Audit và migrate:
1. Grep tất cả `*/assets/pages/*.json` chứa `"slotName": "subside"`.
2. Confirm template `three-column-fixed` đã rename slot tương ứng.
3. Replace ở từng file, giữ blockId/key để không reset admin config.
4. Update `HOW_TO_CREATE_LAYOUT_BLOCK.md` và `docs/src/pages/frontend/layout.mdx`.
5. Generate migration note cho release notes.
```

### 9.3 Refactor: extract block dùng chung

**Prompt:**

```
3 module (blog, video, photo) đang có block listing nhìn na ná nhau, mỗi cái khoảng 80 dòng code.

Hãy:
1. Identify props chung giữa 3 block.
2. Tạo 1 base block `core.block.contentBrowse` (hoặc generic block ở `metafox-core`).
3. Refactor 3 block kia thành extend base + override contentType + itemView.
4. Run tests, ensure visual không thay đổi (compare layout JSON).
```

---

## 10. Checklist trước khi PR (delegate cho AI review)

**Prompt:**

```
@<path-to-PR-files>

Review PR layout này theo checklist (giống `HOW_TO_CREATE_LAYOUT_BLOCK.md` §12):

- [ ] Block file ở `packages/<module>/src/blocks/<PascalCase>/Block.tsx`.
- [ ] JSDoc header có `@type: block`, name, title, keywords, description.
- [ ] `name` follow `<module>.block.<Name>`, unique repo-wide.
- [ ] `defaults` chỉ chứa props admin nên edit, `overrides` lock phần còn lại.
- [ ] `authRequired`, `showWhen`, `privacyWhen` set đúng.
- [ ] `dataSource.apiUrl` + `pagingId` filled cho list paginated.
- [ ] Item view có skeleton tương ứng.
- [ ] Đã add vào ít nhất 1 layout JSON hoặc note "AdminCP-only placement".
- [ ] String qua `i18n.formatMessage` + key có trong messages.json.
- [ ] testid prefix đúng convention.
- [ ] `pnpm lint && pnpm test` pass.

Liệt kê item nào còn thiếu, propose patch.
```

---

---

## 11. Form Builder (JSON schema + `@metafox/form` + `@metafox/json2yup`)

Form ở MetaFox là **schema-driven**: viết JSON schema, FormBuilder render `formik` + MUI fields, validation tự sinh từ `json2yup`.

### 11.1 Tạo form mới từ schema

**Prompt:**

```
@docs/src/pages/frontend/form.mdx

Tạo form `<feature>` (ví dụ: blog create, user profile edit):
- File schema: `frontend/packages/metafox/<module>/src/forms/<formName>.json`
- Component: `frontend/packages/metafox/<module>/src/forms/<FormName>.tsx`
  dùng `<Form schema={...} initialValues={...} />` từ `@metafox/form`.
- Schema phải có:
  - component: "Form", method, action (REST endpoint), submitAction (saga action),
    title, acceptPageParams, value, validation (object schema kiểu json2yup),
    elements (Container chứa các field con).
- Mỗi field dùng đúng component name: `Text | Password | Switch | Checkbox |
  RadioGroup | Select | Datetime | Tags | Privacy | Submit | Button | LinkButton |
  Hidden | Container | Captcha | Editor | Attachment | TypeCategory | FriendPicker |
  ItemPhoto | ItemPhotoGallery | Location | SearchBox | VideoUrl`.
- testid prefix `field <name>` để E2E test target.
- Label/placeholder dùng phrase key `[bracketed]` (FormBuilder sẽ resolve qua i18n).
- Hỗ trợ ref-field validation nếu có (vd: confirmPassword `min: { ref: 'password' }`).
```

### 11.2 Custom form element mới

**Prompt:**

```
@frontend/packages/framework/metafox-form/src/Element.tsx
@frontend/packages/framework/metafox-form-elements/

Tạo form element mới `form.element.<Name>`:
- Header `@type: formElement`, name `form.element.<Name>`.
- Implement `({ field, form, ...rest }) => JSX` dùng `useField` từ formik.
- Wrap MUI primitive matching project style (Text/Select/Datetime).
- Expose props chuẩn: name, label, placeholder, required, disabled, readOnly,
  variant, margin, fullWidth, sxFieldWrapper, description, testid.
- Nếu cần fetch data: dùng saga side-effect, không gọi axios trực tiếp.
- Add unit test, story example.
```

### 11.3 Validation phức tạp với `when`

**Prompt:**

```
@docs/src/pages/frontend/validation.mdx

Schema cần validate field `<name>` phụ thuộc vào field khác:
- Khi `<otherField>` is `<value>` → required + minLength X.
- Khi không → maxLength 0 / nullable.

Tạo schema dùng `when` array (rule name, fields, is, then, otherwise) theo
docs/src/pages/frontend/validation.mdx §When.
```

### 11.4 Submit form qua saga

**Prompt:**

```
@frontend/packages/framework/metafox-form/src/sagas/

Wire form `<formName>`:
- submitAction: `@<feature>/create` (Redux action).
- Tạo saga `frontend/packages/metafox/<module>/src/sagas/<feature>/createItem.ts`
  với header `@type: saga`, takeLatest action.
- Saga: yield apiClient request to action.payload.action, dispatch success/failure.
- Handle 422 validation errors từ Laravel → set formik errors qua setSubmitting + setErrors.
- Show toast / dialog feedback.
```

---

## 12. Dialogs (`dialogBackend` + `useDialog`)

### 12.1 Tạo dialog component mới

**Prompt:**

```
@docs/src/pages/frontend/dialog.mdx

Tạo dialog `<module>.dialog.<purpose>`:
- File: `frontend/packages/metafox/<module>/src/dialogs/<PascalCaseName>/index.tsx`.
- Header `@type: dialog`, name `<module>.dialog.<purpose>`.
- Dùng MUI Dialog primitives từ `@metafox/dialog` (Dialog, DialogTitle,
  DialogContent, DialogActions, Button).
- Bên trong: `const { useDialog } = useGlobal();` →
  `const { setDialogValue, dialogProps } = useDialog();`.
- onSubmit → `setDialogValue(true)`. onCancel → `setDialogValue()`.
- i18n cho mọi label.
```

### 12.2 Present alert / confirm tại chỗ

**Prompt:**

```
Trong saga / handler `<location>`:

Show confirm trước khi xóa item:
const ok = yield apiClient ? null : null;  // skip
const { dialogBackend, i18n } = yield* getGlobalContext();
const ok: boolean = yield dialogBackend.confirm({
  title: i18n.formatMessage({ id: 'delete_item_confirm_title' }),
  message: i18n.formatMessage({ id: 'delete_item_confirm_message' }),
  positiveButton: { label: i18n.formatMessage({ id: 'delete' }), color: 'error' },
  negativeButton: { label: i18n.formatMessage({ id: 'cancel' }) }
});
if (!ok) return;

Implement và clean up tất cả `window.confirm` / `alert()` còn sót lại.
```

### 12.3 Dialog form (form bên trong dialog)

**Prompt:**

```
Tạo dialog hiển thị form ở dạng modal:
- Dialog component dùng `<Form schema={...} onSubmit={...} />` ở DialogContent.
- onSubmit của Form gọi setDialogValue(formValues) để return về caller.
- dialogBackend.present({ component, props }) trả về promise resolve(formValues).
- Caller: const result = await dialogBackend.present(...); if (result) { ... }
```

---

## 13. Routing & Modal Routes

### 13.1 Route component thường

**Prompt:**

```
@docs/src/pages/frontend/routing.mdx

Tạo route page `<module>.<purpose>`:
- File: `frontend/packages/metafox/<module>/src/pages/<PageName>.tsx`.
- Header `@type: route`, name `<module>.<page-name>`,
  path `/<path>/:param(\d+)/:slug?`, chunkName `pages.<module>`, bundle: web.
- Render `<Page pageName="<json-page-name>" pageParams={...}/>` từ `@metafox/layout`.
- Build pageParams qua `createPageParams(props, prev => ({ module_name, item_type, identity }))`.
- Hỗ trợ logged-in vs visitor variants nếu cần.
```

### 13.2 Modal route (view item trong overlay)

**Prompt:**

```
@docs/src/pages/frontend/routing.mdx (§Modal Route)

Tạo modal route `<resource>.viewModal`:
- File: `frontend/packages/metafox/<module>/src/pages/<Name>ViewModal.tsx`.
- Header `@type: modalRoute`, name `<resource>.viewModal`,
  path `/<resource>/:id(\\d+)/:slug?`, chunkName `pages.<module>`, bundle: web.
- Dùng `createViewItemModal({ appName, resourceName, pageName, component })`.
- component trỏ tới dialog đã đăng ký (`<resource>.dialog.<resource>View`).
- Đảm bảo cùng path có Page route fallback khi user share link / refresh.
```

### 13.3 Slug alias (canonical URL resolver)

**Prompt:**

```
URL `/jack` cần resolve thành `/user/profile/1` mà không reload.

Tham chiếu workflow trong @docs/src/pages/frontend/routing.mdx:
1. Add resource resolver in saga (call backend `/core/resolve-slug?path=...`).
2. Trên router miss match: hold previous matched route, show progress bar.
3. Khi resolved: replaceState giữ pathname, re-run router.
4. Cache canonical lookup để tránh request trùng.

Lưu ý: KHÔNG dựa vào browser location ở component logic — đọc qua
`usePageParams` để có $PageContext.
```

---

## 14. Theme & Styling

### 14.1 Đăng ký theme mới

**Prompt:**

```
@docs/src/pages/frontend/theme.mdx
@frontend/packages/metafox/theme-flatten/

Tạo theme `<name>`:
- File: `frontend/packages/metafox/theme-<name>/src/index.tsx`.
- Header `@type: theme`, name `<name>`, system: false (true cho built-in),
  bundle: web.
- Export config object: originBlockLayouts, originGridLayouts, originItemLayouts,
  originTemplates, originPageLayouts, originSiteBlocks, blockLayouts, gridLayouts,
  itemLayouts, templates, pageLayouts, siteBlocks, pageSizesPriority,
  processors, styles.
- styles.json: variables theo schema (default + dark) — palette, typography,
  breakpoints, gutter, shape.borderRadius, blockShadow, avatarSize, appBarHeight, ...
- processors/: hàm overridesComponents(theme), overridesGlobalStyles(theme),
  modify theme.components.* (MuiButton, MuiDialog, MuiSkeleton, MuiCssBaseline).
```

### 14.2 Override một MUI component xuyên theme

**Prompt:**

```
@frontend/packages/metafox/theme-flatten/src/processors/Components.ts

Override MuiButton để default size là medium + borderRadius 4px:
- Add to theme.components.MuiButton.{defaultProps, styleOverrides, variants}.
- Variants riêng cho `Block` button (variant: 'block').
- Confirm dark mode vẫn đúng contrast (test palette.button.hover).
- Match phong cách neighbor processors.
```

### 14.3 Styled component theo MetaFox convention

**Prompt:**

```
Trong `<path-to-component>/styles.ts`:

Convert inline `sx` props phức tạp thành `styled()` helpers:
- Dùng MUI `styled('span', { name: '<ComponentName>', slot: '<SlotName>' })`.
- Truy cập theme.palette / theme.spacing thay vì hardcode hex / px.
- Export named styled component, KHÔNG default export.
- Nếu prop drilling visual variant: dùng shouldForwardProp pattern.
```

### 14.4 Dark mode aware component

**Prompt:**

```
Component `<name>` cần render khác giữa light/dark:
- Dùng `useTheme()` từ MUI / `@metafox/framework/theme`.
- `theme.palette.mode === 'dark'` rẽ nhánh visual.
- Hoặc define MuiXxx.variants trong processors và pass `variant="<name>"`
  để theme tự lo (ưu tiên cách này).
- Không gọi window.matchMedia trực tiếp.
```

---

## 15. Sagas (redux-saga + `@metafox/framework`)

### 15.1 Saga mới với MetaFox header

**Prompt:**

```
@docs/src/pages/frontend/sagas.mdx
@frontend/.cursor/rules/sagas-services.mdc

Tạo saga `<resource>.saga.<purpose>`:
- File: `frontend/packages/metafox/<module>/src/sagas/<purpose>.ts`.
- Header `@type: saga`, name `<resource>.saga.<purpose>`.
- Dispatch action listener via `takeEvery('@<event>/<resource>', handler)` hoặc
  `takeLatest` cho fetch.
- handler signature: `function* handler({ payload }: LocalAction<{...}>)`.
- Trong handler dùng `yield* getGlobalContext()` lấy apiClient,
  getResourceAction, compactData, dialogBackend, i18n.
- API call: prefer `getResourceAction(resourceName, actionName)` →
  `apiClient.request({ url, method, data })`.
- Sau khi pnpm run reload, saga sẽ tự được include vào bundle.
```

### 15.2 Race / cancel pattern

**Prompt:**

```
@frontend/packages/framework/core/src/sagas/coreSetting.ts

Implement saga fetch nhiều endpoint song song với cancel khi cache hit:
- Dùng `fork`, `race`, `join`, `cancel` từ redux-saga/effects.
- Track AbortController để abort axios khi race winner trả về.
- Reference: `bootstrapSettings` trong coreSetting.ts.
```

### 15.3 Refresh token flow

**Prompt:**

```
@frontend/packages/framework/core/src/sagas/coreRefreshToken.ts

Add refresh token trigger khi 401 từ apiClient:
- Listen action `REFRESH_TOKEN`.
- Call backend `/oauth/token` (Passport refresh grant).
- Update authStore + retry queue requests.
- Logout + redirect login khi refresh thất bại.
```

---

## 16. Services & Manager (`@type: service`)

### 16.1 Class service mới

**Prompt:**

```
@docs/src/pages/frontend/service.mdx
@frontend/.cursor/rules/sagas-services.mdc

Tạo class service `<serviceName>`:
- File: `frontend/packages/metafox/<module>/src/services/<ServiceName>.ts`.
- Header `@type: service`, name `<serviceName>`.
- Class với `bootstrap(manager: Manager): this | void` (KHÔNG arrow function).
- Constructor nhận options từ `app/settings.json["<serviceName>"]`.
- Expose API qua `useGlobal().<serviceName>` cho component và
  `getGlobalContext().<serviceName>` cho saga.
- Add typings vào `module.d.ts` extend `@metafox/framework/Manager` interface.
```

### 16.2 Functional service

**Prompt:**

```
Tạo function service `<helperName>` dùng được trong component và saga:
- Pure function, no side effects.
- Header `@type: service`, name `<helperName>`.
- Export default function.
- Type augment qua module.d.ts: declare module '@metafox/framework/Manager'
  { interface Manager { <helperName>: typeof helper; } }.
```

### 16.3 React component service (dependency-free injection)

**Prompt:**

```
Inject component qua Service Manager để các module khác dùng không cần import:

- File: `<module>/src/services/<Component>.tsx`
- Header `@type: service`, name `<ComponentName>`.
- Default export React component.
- Augment Manager interface: `<ComponentName>?: React.FC<<Props>>`.
- Caller: `const { <ComponentName> } = useGlobal(); return <ComponentName />`.

Use case: Comment list / Reaction picker dùng cross-module.
```

---

## 17. State / Redux

### 17.1 Reducer mới cho module

**Prompt:**

```
Tạo reducer cho module `<module>`:
- File: `frontend/packages/metafox/<module>/src/reducers/<sliceName>.ts`.
- Header `@type: reducer`, name `<module>` (hoặc `<module>.<slice>`).
- Default export reducer function (action, state) => state.
- Type augment GlobalState qua `module.d.ts`:
  declare module '@metafox/framework/Manager'
  { interface GlobalState { <module>?: ModuleState } }.
- Match neighbor patterns (entities/paging/lists) — dùng helpers từ
  @metafox/core/state nếu phù hợp (createPagingReducer, createEntitiesReducer).
```

### 17.2 Paging reducer cho list

**Prompt:**

```
@frontend/packages/framework/metafox-core/src/state/createPagingReducer.ts

Implement paging cho resource `<resource>`:
- Dùng `createPagingReducer({ name, resourceName, actionPrefix })`.
- Hook `useGetPaging('<pagingId>')` ở component.
- Action `<resource>.paging.fetchMore` để load next page.
- pagingId convention: REST URL relative (e.g. `/blog?view=latest`).
```

### 17.3 Entities (normalized)

**Prompt:**

```
Tạo entities slice cho `<resource>`:
- Header `@type: reducer`, name `entities.<resource>`.
- Match shape: `state.entities.<resource>[id] = ItemShape`.
- Normalization tự xử lý qua middleware sẵn có (xem `@metafox/normalization`).
- Type augment AppState qua module.d.ts.
- Selector convention: `useGetItem('entities.<resource>.<id>')` hoặc
  `useGetItems(identities[])`.
```

---

## 18. i18n & Translations

### 18.1 Thêm phrase key mới

**Prompt:**

```
@docs/src/pages/frontend/translation.mdx

Thêm phrase key cho module `<module>`:
1. Edit `frontend/packages/metafox/<module>/src/messages.json` (locale `en`):
   thêm `"<key>": "<English text>"`.
2. Sử dụng trong component:
   `i18n.formatMessage({ id: '<key>' }, { value: 100 })`.
3. Locale khác (vi, fr, ...) sẽ được override từ AdminCP database
   (`core/translation/<group>/auto/<revision>` API).
4. Confirm Storybook / dev server reload pickup phrase.
```

### 18.2 ICU formatMessage với placeholders

**Prompt:**

```
Component cần render "Showing 1-10 of 245 results":
- Phrase: `"showing_results": "Showing {start}-{end} of {total, plural, one {# result} other {# results}}"`
- Code: `i18n.formatMessage({ id: 'showing_results' }, { start, end, total })`.
- Đảm bảo plural handling đúng cho cả locale có nhiều dạng plural (ru, ar).
```

### 18.3 Date / time format theo locale

**Prompt:**

```
@frontend/packages/framework/metafox-intl/src/momentLocales.ts

Thêm support cho locale mới `<locale>`:
1. Import `moment/locale/<locale>` trong momentLocales.ts.
2. Verify IntlProvider truyền đúng `<locale>` xuống moment.
3. Component dùng `<FormatDate value={date} format="ll" />` —
   hoặc `moment(date).locale(...).format(...)`.
4. Test render: Lunar calendar / RTL nếu locale yêu cầu.
```

### 18.4 X-Language header (sync FE ↔ BE locale)

**Prompt:**

```
@frontend/packages/framework/metafox-rest-client/src/RestClient.tsx

Verify RestClient đang gửi `X-Language` header từ cookie `userLanguage`:
- Backend Localization middleware đọc header trước user/cookie.
- Confirm khi user đổi locale: cookie set xong → request kế tiếp gửi đúng header.
- Test: API trả phrase đã localize.
```

---

## 19. Cookies & Local Storage

### 19.1 Sử dụng `cookieBackend`

**Prompt:**

```
@docs/src/pages/frontend/cookie.mdx

Trong component / saga, đọc/ghi cookie:

// Component
const { cookieBackend } = useGlobal();
const lang = cookieBackend.get('userLanguage');
cookieBackend.set('userLanguage', 'vi');

// Saga
const { cookieBackend } = yield* getGlobalContext();
const userId = cookieBackend.getInt('user_id');

Best practice: KHÔNG lưu data lớn vào cookie (gửi trong mọi request).
Dùng localStore cho data > vài KB hoặc data không cần server.
```

### 19.2 Local store với JSON

**Prompt:**

```
@docs/src/pages/frontend/local-store.mdx

Persist UI preference (sidebar collapsed, recently viewed items):

const { localStore } = useGlobal();
localStore.set('ui.sidebar.collapsed', '1');
const data = localStore.getJSON('recent.items') ?? [];
localStore.set('recent.items', JSON.stringify([...data, newItem].slice(-20)));

Convention key: prefix `<MFOX_LOCAL_PREFIX>.<feature>.<key>` để tránh collision.
Khi schema thay đổi: bump version key (`recent.items.v2`) thay vì migrate.
```

---

## 20. TypeScript augmentation

### 20.1 Extend Manager / GlobalState

**Prompt:**

```
@docs/src/pages/frontend/typings.mdx

Thêm typings cho service/state mới:

// frontend/packages/metafox/<module>/src/module.d.ts
import '@metafox/framework/Manager';
import { ModuleState } from './types';
import MyService from './services/MyService';

declare module '@metafox/framework/Manager' {
  interface Manager {
    myService?: MyService;
    MyComponent?: React.FC<MyComponentProps>;
  }
  interface GlobalState {
    <module>?: ModuleState;
  }
}

Yêu cầu:
- module.d.ts ở root src/ của package.
- Đảm bảo tsconfig include path đúng.
- Optional `?` cho service nếu user có thể opt-out package.
```

### 20.2 Item shape & resource types

**Prompt:**

```
Tạo shape types cho resource `<resource>`:
- File: `frontend/packages/metafox/<module>/src/types.ts`.
- Define `<Resource>ItemShape extends ItemShape` (từ @metafox/framework).
- Các field match đúng JsonResource backend (snake_case → camelCase nếu BE đã transform).
- Export từ index.ts để các module khác consume.
- Augment AppState.entities.<resource> = Record<string, <Resource>ItemShape>.
```

---

## 21. When conditions (`showWhen` / `privacyWhen`)

Rule engine để hide/show block, menu item, action mà không phải code branch.

### 21.1 Block hiển thị conditional

**Prompt:**

```
@docs/src/pages/frontend/when-lib.mdx

Trong layout JSON / menu config, set:

"showWhen": ["truthy", "setting.chatplus.server"]

Hỗ trợ rules:
truthy / falsy / equals / notEquals / strictEquals / notStrictEquals /
lessThan / lessOrEquals / greater / greaterOrEquals /
lengthEquals / lengthGreater / lengthLess / lengthGreaterOrEquals / lengthLessOrEquals /
oneOf / exists / notExists.

Nested: ["and", [...], [...]] / ["or", [...], [...]] / ["not", [...]].
```

### 21.2 Menu action chỉ hiển khi quyền đủ

**Prompt:**

```
Action menu của blog detail chỉ hiện "Approve" khi pending + có quyền:

"showWhen": [
  "and",
  ["truthy", "item.is_pending"],
  ["truthy", "item.extra.can_approve"]
]

Apply cho file backend menu config (PHP) hoặc frontend menu JSON tương ứng.
```

### 21.3 Privacy gating

**Prompt:**

```
Block "Friends only" feed chỉ hiện cho user logged in + đã có ít nhất 1 friend:

"privacyWhen": [
  "and",
  ["truthy", "user.id"],
  ["greater", "user.statistic.total_friend", 0]
]

Hoặc dùng `authRequired: true` nếu chỉ cần check logged in.
```

---

## 22. Annotation cheat sheet (`@type:` map)

Build-time scanner đọc các JSDoc comment ở đầu file. Đây là tổng hợp toàn bộ `@type:` MetaFox hỗ trợ:

| `@type` | Mục đích | Naming pattern |
|---------|----------|----------------|
| `ui` | Generic React component | `<Component name>` |
| `block` | Configurable block đặt vào slot | `<module>.block.<Name>` |
| `itemView` | Grid/list item view | `<resource>.itemView.<variant>` |
| `embedView` | Embed item view (feed/notif/search) | `<resource>.embedItem.<variant>` |
| `skeleton` | Loading placeholder của itemView/block | `<itemView name>.skeleton` |
| `dialog` | Dialog component | `<module>.dialog.<purpose>` |
| `popover` | Popover component | `<module>.popover.<purpose>` |
| `formElement` | Custom form field | `form.element.<Name>` |
| `route` | Page route | `<module>.<page-name>` |
| `modalRoute` | Modal route (overlay) | `<resource>.viewModal` |
| `saga` | Redux saga module | `<resource>.saga.<purpose>` |
| `reducer` | Redux reducer | `<module>` hoặc `entities.<resource>` |
| `service` | Service / function / component injected vào Manager | `<serviceName>` |
| `theme` | Theme registration | `<themeName>` |

**Best practice:**

- Annotation **MUST** ở dòng đầu file (trước imports nếu cần).
- Mỗi file 1 component/declaration.
- Sau khi thêm/xóa file: `pnpm run reload` để regenerate bundles.

---

## Tham chiếu nhanh (cheat sheet cho AI)

| Cần làm | Đọc trước file |
|---------|---------------|
| `createBlock` HOC | `frontend/packages/framework/metafox-core/src/hocs/createBlock.tsx` |
| Layout primitives (`Block`, `BlockHeader`, ...) | `frontend/packages/framework/metafox-layout/src/LayoutBlock/` |
| Reference listing block | `frontend/packages/metafox/blog/src/blocks/BrowseBlogs/Block.tsx` |
| Reference detail block | `frontend/packages/metafox/blog/src/blocks/ViewBlog/Block.tsx` |
| Reference bespoke block | `frontend/packages/metafox/saved/src/blocks/SidebarTypeFilter/Block.tsx` |
| Sample page JSON | `frontend/packages/metafox/core/src/assets/pages/home.member.json` |
| Layout docs | `docs/src/pages/frontend/layout.mdx`, `docs/HOW_TO_CREATE_LAYOUT_BLOCK.md` |
| Naming patterns | `HOW_TO_CREATE_LAYOUT_BLOCK.md` §8 |
| Responsive fallback table | `HOW_TO_CREATE_LAYOUT_BLOCK.md` §9 |
| Form schema reference | `docs/src/pages/frontend/form.mdx` |
| Form Builder source | `frontend/packages/framework/metafox-form/src/FormBuilder.tsx` |
| Form elements catalog | `frontend/packages/framework/metafox-form-elements/` |
| `json2yup` validation rules | `docs/src/pages/frontend/validation.mdx` |
| Dialog API | `docs/src/pages/frontend/dialog.mdx`, `frontend/packages/framework/metafox-dialog/` |
| Routing & modal routes | `docs/src/pages/frontend/routing.mdx` |
| Theme structure | `docs/src/pages/frontend/theme.mdx`, `frontend/packages/metafox/theme-flatten/` |
| Saga conventions | `docs/src/pages/frontend/sagas.mdx`, `frontend/.cursor/rules/sagas-services.mdc` |
| Service / Manager | `docs/src/pages/frontend/service.mdx` |
| RestClient (apiClient) | `frontend/packages/framework/metafox-rest-client/src/RestClient.tsx` |
| i18n / phrase system | `docs/src/pages/frontend/translation.mdx`, `frontend/packages/framework/metafox-intl/` |
| Cookie / Local storage | `docs/src/pages/frontend/cookie.mdx`, `docs/src/pages/frontend/local-store.mdx` |
| TypeScript augmentation | `docs/src/pages/frontend/typings.mdx` |
| Paging reducer helper | `frontend/packages/framework/metafox-core/src/state/createPagingReducer.ts` |
| When rules library | `docs/src/pages/frontend/when-lib.mdx` |
| Annotation full reference | §22 ở trên + `docs/src/pages/frontend/concepts.mdx` |

---

*Phiên bản: 5.x. Cập nhật: 5/2026. Khi MetaFox đổi convention, cập nhật doc nguồn (`docs/src/pages/frontend/*.mdx` hoặc `HOW_TO_CREATE_LAYOUT_BLOCK.md`) trước, file này tự động chính xác theo.*
