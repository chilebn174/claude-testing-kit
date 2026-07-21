# 🐞 Bug Report: Chuyển tiếp tin nhắn có mention từ group sang group → mention hiển thị sai (raw format)

| Trường | Giá trị |
|---|---|
| **Mã bug** | BUG-006 |
| **Module** | Chat > Forward Message (Chuyển tiếp) / Mention Rendering |
| **Ngày phát hiện** | 21/07/2026 |
| **Người báo cáo** | Nguyễn Thị Diệu Mơ (lethikimchi170499@gmail.com) |
| **Mức độ nghiêm trọng (Severity)** | 🔴 High |
| **Mức độ ưu tiên (Priority)** | 🔴 High |
| **Trạng thái** | Open |

---

## 1. Mô Tả Tóm Tắt

Khi chuyển tiếp (forward) một tin nhắn có chứa **mention (@tên người dùng)** từ nhóm này sang nhóm/cuộc trò chuyện khác, tin nhắn chuyển tiếp hiển thị mention **sai định dạng** — thay vì render thành mention tag/chip đẹp (ví dụ `@Nguyễn Thị Diệu Mơ` có màu, có thể click), tin nhắn hiển thị **nguyên dạng raw nội bộ**:

```
[@Nguyễn Thị Diệu Mơ:7267155587449191]
```

(tên người dùng + user ID phân cách bởi dấu `:`, bọc trong dấu ngoặc vuông) thay vì chỉ hiển thị `@Nguyễn Thị Diệu Mơ`.

## 2. Môi Trường

- **Ứng dụng:** Chat app (desktop web client)
- **Cuộc trò chuyện nguồn:** Chat 1-1 với "Hiệp Hào Ngô" — tin nhắn gốc: `5 @Nguyễn Thị Diệu Mơ` (đã sửa lúc 15:52 — *"Đã sửa"*)
- **Cuộc trò chuyện đích:** Nhóm **"test 123"**

## 3. Điều Kiện Tiên Quyết (Preconditions)

- Có sẵn 1 tin nhắn chứa mention (@tên người dùng) trong một cuộc trò chuyện (group hoặc 1-1).
- Tin nhắn đó (trong bằng chứng đính kèm) đã từng bị **edit** trước khi forward.

## 4. Các Bước Tái Hiện (Steps to Reproduce)

1. Trong chat với "Hiệp Hào Ngô", gửi tin nhắn có mention: `@Nguyễn Thị Diệu Mơ` (tin nhắn hiển thị đúng, có tag "Đã sửa" vì đã edit).
2. Chọn tin nhắn chứa mention đó → chọn **"Chuyển tiếp" (Forward)**.
3. Trong popup "Chuyển tiếp", chọn nhóm đích **"test 123"** → nhập caption "chuyển tiếp" (tuỳ chọn) → nhấn **"Chuyển tiếp"**.
4. Mở nhóm **"test 123"**, quan sát tin nhắn vừa được chuyển tiếp tới.

## 5. Kết Quả Mong Đợi (Expected Result)

Tin nhắn chuyển tiếp trong nhóm "test 123" phải hiển thị mention **đúng định dạng** như tin nhắn gốc — ví dụ: `Tin nhắn chuyển tiếp` kèm nội dung `@Nguyễn Thị Diệu Mơ` được render thành mention tag (có highlight/màu, click được), **không** lộ ra ID nội bộ của user.

## 6. Kết Quả Thực Tế (Actual Result)

Tin nhắn chuyển tiếp hiển thị **nguyên dạng chưa được parse**:

```
Tin nhắn chuyển tiếp
[@Nguyễn Thị Diệu Mơ:7267155587449191]
```

Lỗi này lặp lại ở **cả 2 lần forward** được thực hiện trong bằng chứng đính kèm (2 tin nhắn chuyển tiếp liên tiếp lúc 16:07 và 16:08 đều bị lỗi giống nhau).

## 7. Giả Thuyết Nguyên Nhân (Cần Dev Xác Nhận)

- Cơ chế forward có thể đang **copy raw nội dung mention token** (dạng lưu trữ nội bộ `[@displayName:userId]`) sang tin nhắn mới **mà không chạy qua bước parse/render mention** giống như khi hiển thị tin nhắn thường.
- Có thể pipeline render tin nhắn "forwarded" (có nhãn "Tin nhắn chuyển tiếp") dùng component/logic khác với tin nhắn thường, và component đó chưa hỗ trợ parse cú pháp mention.
- Khả năng liên quan đến việc tin nhắn gốc **đã bị edit** trước khi forward — cần kiểm tra thêm liệu forward tin nhắn có mention nhưng **chưa từng edit** có bị lỗi tương tự không (xem AMB-01).

## 8. Ảnh Hưởng (Impact)

- **UX kém nghiêm trọng:** Người nhận thấy nội dung mention hiển thị lộn xộn, khó đọc.
- **Rò rỉ thông tin nội bộ:** User ID (`7267155587449191`) — vốn là dữ liệu hệ thống — bị lộ ra ngoài giao diện người dùng, tin nhắn chuyển tiếp mất luôn tính năng mention (không click được, không trigger notification cho người được mention trong nhóm đích).

## 9. Thông Tin Cần Bổ Sung (Ambiguities)

| # | Câu hỏi | Mức độ |
|---|---|---|
| AMB-01 | Nếu tin nhắn gốc có mention **chưa từng bị edit**, forward có bị lỗi tương tự không? (Xác định lỗi có liên quan đến flow "edit message" hay xảy ra với mọi mention khi forward) | 🔴 High |
| AMB-02 | Lỗi có xảy ra khi forward tin nhắn có mention từ **group → group** (không qua chat 1-1) không, hay chỉ riêng case "1-1 → group" như trong bằng chứng? | 🟡 Medium |
| AMB-03 | Người được mention (`Nguyễn Thị Diệu Mơ`) trong nhóm đích "test 123" có nhận được notification/Badge mention nào không, hay hoàn toàn không trigger vì mention không được parse? | 🟡 Medium |
| AMB-04 | Forward tin nhắn có mention **@all** hoặc **@Cả nhóm** có bị lỗi tương tự không (khác với mention cá nhân)? | 🟢 Low |

## 10. Đề Xuất Kiểm Thử Bổ Sung (Regression)

1. Forward tin nhắn có mention cá nhân **chưa từng edit** từ 1-1 → group → kiểm tra hiển thị.
2. Forward tin nhắn có mention cá nhân từ **group → group** → kiểm tra hiển thị.
3. Forward tin nhắn có mention **@all / @Cả nhóm** → kiểm tra hiển thị.
4. Forward tin nhắn có **nhiều mention** cùng lúc (2-3 người) → kiểm tra tất cả mention có bị lỗi raw format không.
5. Kiểm tra người được mention trong tin nhắn forward có nhận Badge/notification đúng không (liên hệ BUG-005 — logic mention/badge).
6. Forward cùng 1 tin nhắn đến **nhiều đích** (group, 1-1) → kiểm tra lỗi có nhất quán ở mọi đích.

## 11. Đính Kèm

3 screenshot đính kèm trong báo cáo gốc:
1. Giao diện nhóm "test 123" — hiển thị các poll đang có trong nhóm (ngữ cảnh trước khi forward).
2. Popup "Chuyển tiếp" — đang forward tin nhắn `@VF Em Mơ AUTO` từ chat "Hiệp Hào Ngô" sang nhóm "test 123".
3. Nhóm "test 123" sau khi nhận tin nhắn chuyển tiếp — **2 tin nhắn** hiển thị lỗi raw format `[@Nguyễn Thị Diệu Mơ:7267155587449191]` (khoanh đỏ).
