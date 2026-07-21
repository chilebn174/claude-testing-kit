# 🐞 Bug Report: Edit tin nhắn chưa đọc để thêm mention current user không làm tăng Badge

| Trường | Giá trị |
|---|---|
| **Mã bug** | BUG-005 |
| **Module** | Chat > Message Edit / Mention Notification Badge |
| **Ngày phát hiện** | 21/07/2026 |
| **Người báo cáo** | Nguyễn Thị Diệu Mơ (lethikimchi170499@gmail.com) |
| **Mức độ nghiêm trọng (Severity)** | 🟡 Medium *(tạm xếp — cần xác nhận impact)* |
| **Mức độ ưu tiên (Priority)** | 🟡 Medium |
| **Trạng thái** | Open |

---

## 1. Mô Tả Tóm Tắt

Khi một tin nhắn **chưa đọc (unread)** được người gửi **edit** để thêm **mention (@current user)** vào nội dung, **Badge số lượng mention/thông báo của current user không tăng lên 1** như kỳ vọng — mặc dù sau khi edit, tin nhắn đó thực chất đã chứa mention tới current user.

## 2. Môi Trường

- **Ứng dụng:** Chat app (group chat)
- **Tính năng liên quan:** Edit message, Mention (@), Badge đếm số thông báo/mention chưa đọc

## 3. Điều Kiện Tiên Quyết (Preconditions) — *nháp, cần xác nhận*

- Current user là thành viên trong nhóm, **chưa đọc** một tin nhắn cụ thể (tin nhắn nằm sau vị trí "last read" của current user).
- Tin nhắn đó **ban đầu không mention** current user.
- Người gửi khác **edit** tin nhắn đó, thêm **@current_user** vào nội dung.

## 4. Các Bước Tái Hiện (Steps to Reproduce) — *nháp, cần xác nhận lại với người báo cáo*

1. Member khác (không phải current user) gửi 1 tin nhắn trong nhóm, **không mention** current user.
2. Current user **chưa đọc** tin nhắn này (không mở/không scroll tới tin nhắn đó).
3. Member đó **edit** lại tin nhắn, thêm **@current_user** vào nội dung.
4. Quan sát **Badge** (số đếm mention/thông báo) của current user — kiểm tra có tăng thêm 1 hay không.

## 5. Kết Quả Mong Đợi (Expected Result)

Sau khi tin nhắn (đang ở trạng thái chưa đọc) được edit để thêm mention current user, Badge đếm mention/thông báo của current user phải **tăng thêm 1**, tương đương như khi nhận một mention mới, vì đối với current user đây là lần đầu tin nhắn đó chứa mention tới họ.

## 6. Kết Quả Thực Tế (Actual Result)

Badge **không tăng** sau khi tin nhắn được edit thêm mention — current user không được thông báo về mention mới này dù tin nhắn (mà họ chưa đọc) giờ đã chứa mention tới họ.

## 7. Giả Thuyết Nguyên Nhân (Cần Dev Xác Nhận)

- Logic tính Badge/mention count có thể chỉ chạy tại thời điểm **tạo tin nhắn (create)**, không re-evaluate khi tin nhắn được **edit/update** → mention thêm vào sau khi edit không được backend/client nhận diện là mention mới cần đếm.
- Có thể hệ thống coi "mention" là bất biến theo message ID kể từ lúc tạo, chưa xử lý case nội dung mention thay đổi qua chỉnh sửa.

## 8. Ảnh Hưởng (Impact)

Current user có thể bỏ lỡ hoàn toàn việc bị mention nếu chỉ dựa vào Badge để biết có ai nhắc đến mình — đặc biệt rủi ro trong các nhóm đông thành viên/nhiều tin nhắn, nơi user thường chỉ chú ý khi Badge tăng.

## 9. Thông Tin Cần Bổ Sung (Ambiguities)

| # | Câu hỏi | Mức độ |
|---|---|---|
| AMB-01 | Nếu tin nhắn đó **đã được current user đọc rồi** mới edit thêm mention, Badge có tăng đúng không? (giúp xác định bug có riêng cho case "unread" hay không) | 🔴 High |
| AMB-02 | Sau khi mở tin nhắn (đánh dấu đã đọc) sau khi edit, mention có được ghi nhận (ví dụ trong danh sách "Mentions") dù Badge không tăng lúc edit không? | 🔴 High |
| AMB-03 | Trường hợp ngược lại: edit để **xóa** mention khỏi tin nhắn đã có mention chưa đọc — Badge có giảm đúng không? | 🟡 Medium |
| AMB-04 | Có screenshot/video minh họa không? | 🟡 Medium |

## 10. Đề Xuất Kiểm Thử Bổ Sung (Regression)

1. Gửi tin nhắn không mention, current user chưa đọc → edit thêm mention → kiểm tra Badge tăng 1.
2. Gửi tin nhắn không mention, current user **đã đọc** → edit thêm mention → kiểm tra Badge có tăng (mention mới dù message cũ).
3. Gửi tin nhắn có mention current user, chưa đọc → edit **xóa** mention → kiểm tra Badge có giảm tương ứng.
4. Edit tin nhắn nhiều lần (thêm rồi xóa rồi thêm lại mention) → kiểm tra Badge cuối cùng có đúng số lượng thực tế không.
5. Kiểm tra danh sách "Mentions"/thông báo có ghi nhận đúng tin nhắn đã edit hay không, độc lập với Badge.

## 11. Đính Kèm

*(Chưa có screenshot/video đính kèm cho bug này — khuyến nghị bổ sung để xác định chính xác hành vi lỗi.)*
