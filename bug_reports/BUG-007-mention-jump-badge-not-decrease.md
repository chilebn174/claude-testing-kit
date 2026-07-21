# 🐞 Bug Report: Click "@" để jump tới tin nhắn mention — số lượng mention không giảm, jump tới mention mới nhất không ẩn "@"

| Trường | Giá trị |
|---|---|
| **Mã bug** | BUG-007 |
| **Module** | Chat > Mention Indicator ("@" jump button) / Mention Badge |
| **Ngày phát hiện** | 21/07/2026 |
| **Người báo cáo** | Nguyễn Thị Diệu Mơ (lethikimchi170499@gmail.com) |
| **Mức độ nghiêm trọng (Severity)** | 🟡 Medium *(tạm xếp — cần xác nhận impact)* |
| **Mức độ ưu tiên (Priority)** | 🟡 Medium |
| **Trạng thái** | Open |
| **Liên quan** | BUG-005 (edit message thêm mention không tăng Badge) |

---

## 1. Mô Tả Tóm Tắt

Khi cuối hội thoại có **nhiều tin nhắn mention** current user chưa xem, hệ thống hiển thị nút/indicator **"@"** để jump nhanh tới các tin nhắn mention. Khi click vào "@" để nhảy tới từng tin nhắn mention:
1. **Số lượng mention (Badge/counter) không giảm** sau khi đã jump/xem tin nhắn mention đó.
2. Khi jump tới **mention mới nhất (cuối cùng)**, indicator **"@" vẫn không tự ẩn đi** dù đã xem hết tất cả mention.

## 2. Môi Trường

- **Ứng dụng:** Chat app (group chat / 1-1 chat)
- **Tính năng liên quan:** Mention jump indicator ("@" floating button), Mention Badge/counter

## 3. Điều Kiện Tiên Quyết (Preconditions)

- Current user có **nhiều tin nhắn mention** chưa đọc/chưa xem trong 1 cuộc trò chuyện (≥ 2 mention).
- Indicator **"@"** đang hiển thị kèm số lượng mention chưa xem (ví dụ badge số).

## 4. Các Bước Tái Hiện (Steps to Reproduce) — *nháp, cần xác nhận lại*

1. Nhận nhiều tin nhắn mention liên tiếp trong 1 cuộc trò chuyện (ví dụ 3 mention chưa xem).
2. Quan sát indicator **"@"** hiển thị số lượng mention chưa xem (ví dụ "@ 3").
3. Click vào "@" để **jump** tới tin nhắn mention đầu tiên/gần nhất.
4. Kiểm tra số lượng hiển thị trên indicator "@" có **giảm đi 1** sau khi đã jump/xem tin nhắn đó không.
5. Tiếp tục click "@" để jump tới **mention cuối cùng (mới nhất)**.
6. Kiểm tra indicator "@" có **tự ẩn đi** sau khi đã xem hết tất cả mention không.

## 5. Kết Quả Mong Đợi (Expected Result)

- Mỗi lần jump tới 1 tin nhắn mention (và tin nhắn đó được xem/đọc), **số lượng mention trên indicator "@" phải giảm tương ứng**.
- Khi đã jump/xem tới **mention cuối cùng (mới nhất)**, indicator **"@" phải tự ẩn đi** (vì không còn mention nào chưa xem).

## 6. Kết Quả Thực Tế (Actual Result)

- Số lượng mention hiển thị trên indicator "@" **không giảm** sau khi click jump và xem tin nhắn mention.
- Khi jump tới mention mới nhất (cuối cùng), indicator **"@" vẫn còn hiển thị**, không tự ẩn dù đã xem hết.

## 7. Giả Thuyết Nguyên Nhân (Cần Dev Xác Nhận)

- Hành động "jump tới mention" (click "@") có thể chỉ thực hiện **scroll tới vị trí tin nhắn**, chưa trigger đồng thời API/logic **đánh dấu mention đã xem (mark as seen)** để cập nhật lại counter.
- Có thể logic đếm mention đang tính theo **tổng số mention chưa đọc dựa trên "last read position"** của toàn bộ conversation (giống cơ chế unread message), chứ không giảm dần theo từng mention riêng lẻ được xem qua thao tác jump — dẫn đến việc jump không đủ để cập nhật lại count/ẩn indicator.
- Có khả năng liên quan đến cùng lớp logic mention/badge đã ghi nhận ở **BUG-005** (badge không tăng khi edit thêm mention) — cả 2 đều cho thấy việc đồng bộ giữa hành động thực tế (đọc/xem mention) và số đếm hiển thị (Badge/indicator) chưa chính xác.

## 8. Ảnh Hưởng (Impact)

Current user không thể dựa vào indicator "@" để biết chính xác còn bao nhiêu mention chưa xem — gây nhiễu, khiến user phải tự kiểm tra thủ công thay vì tin tưởng vào số đếm hệ thống hiển thị, đặc biệt bất tiện trong nhóm có nhiều mention dồn dập.

## 9. Thông Tin Cần Bổ Sung (Ambiguities)

| # | Câu hỏi | Mức độ |
|---|---|---|
| AMB-01 | Số lượng mention có giảm nếu user **đọc tin nhắn theo cách thông thường** (scroll tay tới, không dùng nút "@" để jump), hay bug chỉ xảy ra riêng với thao tác click "@"? | 🔴 High |
| AMB-02 | Sau khi jump hết tất cả mention, nếu **reload trang** thì indicator "@" và số đếm có tự cập nhật đúng lại không? | 🔴 High |
| AMB-03 | Bug này xảy ra ở **mọi cuộc trò chuyện** (1-1 và group) hay chỉ ở group? | 🟡 Medium |
| AMB-04 | Có screenshot/video minh họa số đếm không giảm và indicator không ẩn không? | 🟡 Medium |

## 10. Đề Xuất Kiểm Thử Bổ Sung (Regression)

1. Có 3 mention chưa xem → click "@" jump từng cái một → kiểm tra số đếm giảm dần đúng theo từng lần jump (3→2→1→0).
2. Jump tới mention cuối cùng → kiểm tra indicator "@" tự ẩn ngay lập tức.
3. So sánh hành vi: đọc mention bằng cách **scroll tay** (không dùng nút "@") vs **click "@" để jump** — xác định bug có ở cả 2 cách hay chỉ riêng jump.
4. Sau khi jump hết mention, reload trang → kiểm tra số đếm/indicator có đúng lại không (0/ẩn).
5. Test với mention đến từ **nhiều cuộc trò chuyện khác nhau** cùng lúc — kiểm tra indicator tổng (nếu có ở màn hình danh sách chat) có cập nhật đúng theo từng conversation.

## 11. Đính Kèm

*(Chưa có screenshot/video đính kèm cho bug này — khuyến nghị bổ sung để xác định chính xác hành vi lỗi của indicator "@" và Badge.)*
