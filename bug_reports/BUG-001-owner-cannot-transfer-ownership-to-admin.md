# 🐞 Bug Report: Owner không thể chuyển quyền chủ nhóm cho thành viên có vai trò Admin

| Trường | Giá trị |
|---|---|
| **Mã bug** | BUG-001 |
| **Module** | Quản lý nhóm chat > Group Members > Transfer Group Owner |
| **Ngày phát hiện** | 21/07/2026 |
| **Người báo cáo** | Nguyễn Thị Diệu Mơ (lethikimchi170499@gmail.com) |
| **Mức độ nghiêm trọng (Severity)** | 🔴 High |
| **Mức độ ưu tiên (Priority)** | 🔴 High |
| **Trạng thái** | Open |

---

## 1. Mô Tả Tóm Tắt

Trong nhóm chat, khi **Owner** mở menu thao tác (`...`) của một thành viên đang giữ vai trò **Admin**, menu chỉ hiển thị 2 tùy chọn **"Send message"** và **"Remove from group"** — **thiếu tùy chọn chuyển quyền chủ nhóm (Transfer group owner)**. Do đó Owner không có cách nào chuyển giao quyền sở hữu nhóm cho một Admin sẵn có thông qua giao diện danh sách thành viên.

## 2. Môi Trường

- **Ứng dụng:** Chat app (desktop client)
- **Nhóm test:** "VF ăn trưa" (6 members)
- **Role user hiện tại:** Owner (Nguyễn Thị Diệu Mơ)
- **Target thành viên:** Hiệp Hào Ngô — role **Admin**

## 3. Điều Kiện Tiên Quyết (Preconditions)

- Tài khoản đăng nhập đang là **Owner** của nhóm.
- Nhóm có ít nhất 1 thành viên khác đã được cấp vai trò **Admin**.

## 4. Các Bước Tái Hiện (Steps to Reproduce)

1. Đăng nhập bằng tài khoản Owner của nhóm.
2. Mở nhóm chat "VF ăn trưa" → click icon thông tin (`ⓘ`) để mở panel **Group members**.
3. Trong danh sách thành viên, xác định thành viên có role **Admin** (ví dụ: Hiệp Hào Ngô).
4. Click icon **"..."** (more options) bên cạnh thành viên đó.
5. Quan sát các tùy chọn hiển thị trong menu.

## 5. Kết Quả Mong Đợi (Expected Result)

Menu phải hiển thị thêm tùy chọn **"Transfer group owner"** (hoặc tương đương) bên cạnh "Send message" và "Remove from group", cho phép Owner chuyển quyền chủ nhóm cho Admin này.

## 6. Kết Quả Thực Tế (Actual Result)

Menu chỉ hiển thị **2 tùy chọn**:
- Send message
- Remove from group

→ **Không có** tùy chọn chuyển quyền chủ nhóm. Owner bị chặn hoàn toàn thao tác này với thành viên role Admin qua UI.

## 7. Ghi Chú Quan Trọng (Đối Chiếu Lịch Sử Chat)

Lịch sử chat trong cùng nhóm cho thấy dòng hệ thống: *"Hiệp Hào Ngô transferred group ownership to you"* — chứng tỏ tính năng **transfer ownership vẫn tồn tại và hoạt động được** trong hệ thống (ít nhất theo chiều Admin/Member → Owner cũ). Điều này gợi ý bug nằm ở **entry point/menu hiển thị** cho chiều ngược lại (Owner → Admin), chứ không hẳn là tính năng chưa được xây dựng.

## 8. Giả Thuyết Nguyên Nhân (Cần Dev Xác Nhận)

- Menu action "Transfer owner" có thể đang bị **điều kiện lọc sai** — chỉ hiển thị khi target có role **Member** thường, vô tình loại trừ luôn các target đã có role **Admin**.
- Hoặc thiếu hẳn case xử lý UI cho target = Admin trong component render menu context.

## 9. Ảnh Hưởng (Impact)

Owner không thể chủ động bàn giao quyền quản trị nhóm cho một Admin đáng tin cậy đã có sẵn trong nhóm — phải dùng vòng qua (ví dụ nhờ Admin đó tự thực hiện hành động tương đương, nếu có) hoặc không thể thực hiện được, ảnh hưởng trực tiếp đến luồng quản trị nhóm.

## 10. Đề Xuất Kiểm Thử Bổ Sung (Regression)

| # | Test case gợi ý |
|---|---|
| 1 | Owner mở menu `...` của thành viên role **Member** (không phải Admin) → kiểm tra "Transfer owner" có hiển thị không |
| 2 | Owner mở menu `...` của thành viên role **Admin** → xác nhận lại bug này |
| 3 | So sánh hành vi giữa nhóm có 1 Admin duy nhất vs nhiều Admin |
| 4 | Kiểm tra API/network request khi hover/click menu — xác nhận option bị ẩn ở FE hay bị chặn ở BE (response không trả field cho phép) |

## 11. Đính Kèm

Screenshot gốc: Group members panel của nhóm "VF ăn trưa", menu context của "Hiệp Hào Ngô - Admin" (đính kèm trong báo cáo gốc).
