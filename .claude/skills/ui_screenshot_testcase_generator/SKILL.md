---
name: UI Screenshot Test Case Generator
description: Skill sinh manual test case trực tiếp từ 1 ảnh chụp giao diện (screenshot) khi không có tài liệu spec hoặc browser live — phân rã ảnh thành component/button, đảm bảo không bỏ sót phần tử nào trước khi sinh case.
---

# UI Screenshot Test Case Generator

## Description

Skill dành cho tình huống chỉ có **1 ảnh chụp màn hình giao diện** (screenshot/mockup/design) làm nguồn duy nhất — không có tài liệu requirement, không có browser thật để inspect DOM. Agent phải tự phân tích ảnh, phân rã thành các thành phần nhỏ, và sinh test case đầy đủ mà **không được bỏ sót bất kỳ button/thành phần chức năng nào** hiển thị trên ảnh.

Khác với `ui_debug_agent` (inspect DOM thật qua Playwright MCP) — skill này áp dụng khi **chỉ có ảnh tĩnh**, không có quyền truy cập UI thật.

---

## When to Use

Sử dụng skill này khi:

- User gửi 1 ảnh giao diện (screenshot, mockup, design) và yêu cầu sinh test case
- Không có tài liệu spec kèm theo, hoặc ảnh là nguồn thông tin chính
- Không có browser live để inspect DOM trực tiếp

**KHÔNG** dùng skill này khi:

- Có tài liệu requirement/SRS đầy đủ → dùng `rbt_manual_testing`
- Có browser thật để inspect DOM → dùng `ui_debug_agent`

---

## Quy Trình 5 Bước (BẮT BUỘC tuần tự)

### Bước 1: Phân loại thành phần trên ảnh

Nhìn toàn bộ ảnh, phân loại **mọi thành phần nhìn thấy được** vào đúng 1 trong 2 nhóm:

| Nhóm | Định nghĩa | Ví dụ |
|---|---|---|
| **Button / Action** | Thành phần có thể **thao tác được** (click, tap, toggle...), dẫn tới 1 hành động hoặc điều hướng | Nút bấm, icon có thể click, mục có `>` (điều hướng), toggle, link |
| **Thông tin hiển thị** | Thành phần **chỉ đọc**, không thao tác trực tiếp được | Avatar, tên, số liệu, label, text mô tả |

Ghi rõ danh sách 2 nhóm này ra trước khi làm bước tiếp theo — đây là input cho toàn bộ quy trình sau.

### Bước 2: Sinh case Entry Point

**Luôn là case đầu tiên.** Xác định: màn hình/tính năng trong ảnh được **mở ra bằng cách nào** (từ đâu, thao tác gì để tới được đây)?

- Nếu ảnh không đủ ngữ cảnh để biết entry point → hỏi user thay vì đoán
- Case Entry Point viết theo format chuẩn: Pre-condition / Steps / Expected (mở đúng màn hình, hiển thị đủ nội dung)

### Bước 3: Tách thành component nhỏ

Nhóm các Button/Action lại theo **khu vực chức năng logic** trên ảnh (thường tương ứng với các khối/section được ngăn cách bằng khoảng trắng, đường kẻ, hoặc card riêng biệt trong ảnh). Mỗi component nên có tên ngắn gọn mô tả đúng vai trò của khu vực đó.

### Bước 4: Liệt kê đủ Button trong từng Component — Checklist chống bỏ sót

Trong mỗi component, liệt kê **từng button/thành phần tương tác** riêng lẻ.

**Bắt buộc chống bỏ sót (Anti-Pattern nghiêm trọng nhất của skill này):**
- [ ] Đã quét đủ **toàn bộ vùng ảnh** (trên → dưới, trái → phải), không chỉ phần nổi bật
- [ ] Đã tính cả các **icon nhỏ, nút phụ, mục có `>`, toggle, badge số** — không chỉ button chữ lớn
- [ ] Đối chiếu lại tổng số button đã liệt kê với ảnh gốc **1 lần nữa** trước khi sang Bước 5
- [ ] Nếu ảnh bị cắt/che 1 phần → báo cho user thay vì bỏ qua phần đó

### Bước 5: Sinh Test Case chi tiết cho từng Button

Với mỗi button đã liệt kê ở Bước 4, áp dụng tối thiểu các hướng case sau (bổ sung thêm nếu button có đặc thù riêng):

| Hướng case | Mô tả |
|---|---|
| **Click/Tap hành động chính** | Bấm vào button → verify đúng hành động/điều hướng xảy ra |
| **UI/Label hiển thị đúng** | Text, icon, trạng thái hiển thị (enable/disable) đúng như ảnh |
| **Trạng thái khác nhau** (nếu có) | Button có thể đổi trạng thái theo điều kiện (VD: số liệu thay đổi, badge cập nhật) |

Nếu button dẫn tới 1 màn hình/luồng khác không có trong ảnh hiện tại — chỉ sinh case "điều hướng đúng tới đâu", **không tự bịa** nội dung màn hình đích khi chưa có ảnh/thông tin xác nhận.

Áp dụng **Test Data cụ thể** (theo quy tắc chung CLAUDE.md — không dùng placeholder chung chung).

---

## Anti-Patterns (NGHIÊM CẤM)

| ❌ Sai | ✅ Đúng |
|---|---|
| Chỉ sinh case cho button nổi bật, bỏ qua icon nhỏ | Quét đủ 100% vùng ảnh, liệt kê cả icon phụ |
| Đoán entry point khi ảnh không đủ ngữ cảnh | Hỏi user |
| Bịa nội dung màn hình đích khi button điều hướng qua ảnh chưa có | Chỉ xác nhận "điều hướng đúng", chờ ảnh màn tiếp theo |
| Gộp nhiều button vào 1 case chung | Mỗi button 1 nhóm case riêng theo Bước 5 |
| Sinh case ngay từ ảnh mà không phân loại Bước 1 trước | Luôn phân loại Button/Action vs Thông tin hiển thị trước |

---

## Ví dụ áp dụng (tham khảo)

Ảnh "Thông tin nhóm" (group info panel):

1. **Phân loại (Bước 1):** 8 Button/Action (Tắt thông báo, Ghim, Thêm thành viên, Ảnh/Video/Link&Tài liệu, Danh sách nhắc hẹn, Tin nhắn ghim, Thành viên nhóm, Rời nhóm) + 3 Thông tin hiển thị (Avatar, Tên nhóm, Số thành viên)
2. **Entry Point (Bước 2):** Click icon Thông tin (ℹ️) ở header hội thoại nhóm → mở màn "Thông tin nhóm"
3. **Component (Bước 3):** Header nhóm / Quick Actions / Media Section / List Section / Danger Zone
4. **Checklist (Bước 4):** Đối chiếu đủ 8/8 button, không sót
5. **Test Case (Bước 5):** Sinh case Click + UI hiển thị cho từng button trong 5 component trên

---

## Output

- Danh sách phân loại Button/Action vs Thông tin hiển thị (Bước 1)
- Bảng Test Case theo đúng format chuẩn của project (ID | Sub Function | Test View Point | Test Case Name | Pre-condition | Test Steps | Expected Result | Priority), nhóm theo Component
