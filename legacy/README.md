# Legacy PhpFox documentation

Tài liệu của **PhpFox** (sản phẩm tiền thân của MetaFox, trước khi viết lại bằng Laravel + React).

Lưu trữ ở đây để **tham khảo lịch sử** — khái niệm tên app/menu/quyền, cách block hoạt động ở thế hệ cũ. **KHÔNG** dùng làm nguồn để sinh code MetaFox vì:

- API REST khác (PhpFox dùng SOAP/XML-RPC ở một số chỗ).
- Frontend cũ là PHP-Smarty, không phải React.
- Permission engine khác.
- Module structure khác (`PHPFOX_DIR_MODULE/<module>/include/`).

| File | Format | Mục đích |
|------|--------|----------|
| `phpfox-docs.md` | Markdown text (3.3 MB) | Search/grep nội dung PhpFox docs. |
| `phpfox-docs.pdf` | PDF (34 MB) | Đọc tuần tự + lưu offline. |
| `phpfox-docs.epub` | EPUB (1.2 MB) | Đọc trên máy đọc sách. |

> Khi cần xác nhận một concept tồn tại trong MetaFox hiện tại hay không, **luôn check live codebase** (`backend/packages/`, `frontend/packages/`) trước khi tin theo doc cũ này.
