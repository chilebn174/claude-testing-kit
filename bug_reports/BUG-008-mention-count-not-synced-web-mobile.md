# 🐞 Bug Report: Mention count trên Web không đồng bộ sang Mobile

| Trường | Giá trị |
|---|---|
| **Mã bug** | BUG-008 |
| **Module** | Chat > Mention Badge/Count — Cross-platform Sync (Web ↔ Mobile) |
| **Ngày phát hiện** | 21/07/2026 |
| **Người báo cáo** | Nguyễn Thị Diệu Mơ (lethikimchi170499@gmail.com) |
| **Mức độ nghiêm trọng (Severity)** | 🟡 Medium *(tạm xếp — cần xác nhận impact)* |
| **Mức độ ưu tiên (Priority)** | 🟡 Medium |
| **Trạng thái** | Open |
| **Liên quan** | BUG-005 (edit message thêm mention không tăng Badge), BUG-007 (jump "@" không giảm count) |

---

## 1. Mô Tả Tóm Tắt

Khi current user xem/đọc mention (hoặc số lượng mention thay đổi) trên **client Web**, số đếm mention (mention count/Badge) **không được đồng bộ real-time sang client Mobile** của cùng tài khoản — 2 client hiển thị số mention khác nhau tại cùng một thời điểm.

## 2. Môi Trường

- **Ứng dụng:** Chat app — đa nền tảng (Web client + Mobile client, cùng 1 tài khoản đăng nhập đồng thời)
- **Tính năng liên quan:** Mention count/Badge — đồng bộ trạng thái đọc (read state) giữa các thiết bị

## 3. Điều Kiện Tiên Quyết (Preconditions)

- Cùng 1 tài khoản đăng nhập đồng thời trên **Web** và **Mobile**.
- Có mention chưa đọc, mention count đang hiển thị giống nhau ban đầu trên cả 2 thiết bị.

## 4. Các Bước Tái Hiện (Steps to Reproduce) — *nháp, cần xác nhận lại*

1. Đăng nhập cùng 1 tài khoản trên cả Web và Mobile, đảm bảo cả 2 đang hiển thị **cùng số lượng mention** chưa đọc.
2. Trên **Web**, thực hiện xem/đọc một (hoặc nhiều) tin nhắn mention để làm giảm mention count trên Web (ví dụ từ 3 → 1).
3. Không thao tác gì trên **Mobile** (không mở app / không refresh thủ công).
4. Quan sát mention count hiển thị trên **Mobile** — kiểm tra có tự động cập nhật giảm theo Web hay vẫn giữ số cũ.

## 5. Kết Quả Mong Đợi (Expected Result)

Mention count phải được đồng bộ **real-time giữa các thiết bị** của cùng tài khoản — khi user đọc mention trên Web, count trên Mobile cũng phải giảm tương ứng gần như ngay lập tức (hoặc khi mở lại app).

## 6. Kết Quả Thực Tế (Actual Result)

Mention count trên Mobile **không cập nhật** theo thay đổi đã thực hiện trên Web — 2 thiết bị hiển thị số mention lệch nhau tại cùng thời điểm.

## 7. Giả Thuyết Nguyên Nhân (Cần Dev Xác Nhận)

- Trạng thái "đã xem mention" (read state) có thể chỉ được cập nhật **cục bộ (local state)** trên client Web, không được đồng bộ lên server, hoặc có đồng bộ lên server nhưng Mobile không **subscribe/lắng nghe real-time event** để nhận cập nhật.
- Mobile có thể chỉ fetch lại mention count khi **mở app / vào foreground**, không có cơ chế push update real-time (qua WebSocket/Push Notification) khi có thay đổi từ thiết bị khác.
- Đây có thể là biểu hiện của **cùng lớp vấn đề đồng bộ real-time** đã ghi nhận ở BUG-002 (poll không đồng bộ), BUG-005, BUG-007 (mention count/badge chưa chính xác) — cho thấy cơ chế real-time sync của hệ thống (không riêng gì mention) có vấn đề chung.

## 8. Ảnh Hưởng (Impact)

User dùng đa thiết bị (Web + Mobile) nhận thông tin mention không nhất quán — dễ bị nhầm là "còn tin nhắn mention chưa đọc" trên thiết bị này dù thực ra đã đọc ở thiết bị kia, gây trải nghiệm khó chịu và giảm độ tin cậy vào tính năng Badge.

## 9. Thông Tin Cần Bổ Sung (Ambiguities)

| # | Câu hỏi | Mức độ |
|---|---|---|
| AMB-01 | Nếu **mở lại/reload app Mobile** (thoát vào lại), count có tự cập nhật đúng không? (phân biệt bug real-time thuần túy vs bug dữ liệu sai luôn ở backend) | 🔴 High |
| AMB-02 | Chiều ngược lại — đọc mention trên **Mobile trước**, count trên Web có đồng bộ không, hay bug chỉ 1 chiều Web → Mobile? | 🔴 High |
| AMB-03 | Độ trễ đồng bộ là bao lâu — hoàn toàn không đồng bộ, hay có đồng bộ nhưng chậm (vài giây/phút)? | 🟡 Medium |
| AMB-04 | Bug xảy ra với **mention count tổng** (icon "Mentions" ở danh sách chat) hay cả **Badge theo từng cuộc trò chuyện riêng lẻ**? | 🟡 Medium |
| AMB-05 | Có screenshot/video minh họa số liệu lệch giữa Web và Mobile không? | 🟢 Low |

## 10. Đề Xuất Kiểm Thử Bổ Sung (Regression)

1. Đọc mention trên Web → kiểm tra Mobile có cập nhật real-time (không thao tác gì) trong vài giây.
2. Đọc mention trên Web → mở lại app Mobile (background → foreground / relaunch) → kiểm tra count có đúng lại không.
3. Test chiều ngược lại: đọc mention trên Mobile → kiểm tra Web có đồng bộ đúng không.
4. Test đồng thời cả 2 thiết bị cùng đọc các mention khác nhau → kiểm tra count cuối cùng đồng nhất trên cả 2.
5. Kiểm tra tổng Badge mention ở danh sách chat (tổng toàn hệ thống) so với Badge riêng từng cuộc trò chuyện — xác định lệch ở cấp nào.
6. Kiểm tra log network/push notification giữa Web và Mobile khi có thay đổi mention — xác nhận event có được gửi đi từ server không.

## 11. Đính Kèm

*(Chưa có screenshot/video đính kèm cho bug này — khuyến nghị bổ sung ảnh chụp đồng thời cả 2 màn hình Web/Mobile để đối chiếu số liệu lệch.)*
