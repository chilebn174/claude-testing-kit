# 🐞 Bug Report: Popup Poll không tự động re-render real-time khi có người vừa vote

| Trường | Giá trị |
|---|---|
| **Mã bug** | BUG-004 |
| **Module** | Group Chat > Poll (Bình chọn) > Popup danh sách "Not voted" / "Answers" |
| **Ngày phát hiện** | 21/07/2026 |
| **Người báo cáo** | Nguyễn Thị Diệu Mơ (lethikimchi170499@gmail.com) |
| **Mức độ nghiêm trọng (Severity)** | 🟡 Medium *(tạm xếp — cần xác nhận impact)* |
| **Mức độ ưu tiên (Priority)** | 🟡 Medium |
| **Trạng thái** | Open |
| **Liên quan** | BUG-002 (poll không đồng bộ real-time), BUG-003 (empty state danh sách "Chưa bình chọn" sai) |

---

## 1. Mô Tả Tóm Tắt

Trong popup chi tiết Poll (có 2 tab: **"Not voted"** và **"Answers"**), khi một thành viên vừa thực hiện vote, popup **không tự động re-render real-time** để chuyển thành viên đó từ tab "Not voted" sang tab "Answers". Người xem popup (ví dụ Owner hoặc thành viên khác đang mở sẵn popup) không thấy cập nhật ngay mà vẫn thấy dữ liệu cũ cho đến khi có hành động khác (đóng/mở lại popup, reload...).

## 2. Môi Trường

- **Ứng dụng:** Chat app (group chat), tính năng Poll — popup chi tiết poll với 2 tab "Not voted" / "Answers"
- **Nhóm test:** "VF ăn trưa" — poll "test poll 1" / "test poll 2"

## 3. Điều Kiện Tiên Quyết (Preconditions)

- Có 1 Poll đang active trong nhóm với ít nhất 2 thành viên chưa vote.
- Một thành viên (A, ví dụ Owner) đang **mở sẵn popup** chi tiết poll, đứng ở tab "Not voted".
- Một thành viên khác (B) đang nằm trong danh sách "Not voted" của popup đó.

## 4. Các Bước Tái Hiện (Steps to Reproduce) — *nháp, cần xác nhận lại*

1. Member A mở popup chi tiết Poll, xem tab **"Not voted"** — thấy Member B đang nằm trong danh sách.
2. Member B (trên client khác) thực hiện **vote** cho Poll đó.
3. Member A **không thao tác gì thêm** (không đóng popup, không reload), tiếp tục quan sát popup đang mở.
4. Kiểm tra tab "Not voted" có tự động loại Member B ra không, và tab "Answers" có tự động thêm Member B vào không — **theo thời gian thực (real-time)**, không cần đóng/mở lại popup.

## 5. Kết Quả Mong Đợi (Expected Result)

Ngay khi Member B vote, popup đang mở của Member A phải **tự động re-render real-time**:
- Member B được loại khỏi tab "Not voted".
- Member B xuất hiện trong tab "Answers" (với lựa chọn tương ứng).
- Không cần Member A phải đóng/mở lại popup hoặc reload trang.

## 6. Kết Quả Thực Tế (Actual Result)

Popup đang mở của Member A **không tự re-render** — Member B vẫn hiển thị ở tab "Not voted" (dữ liệu cũ) dù đã vote xong. Việc cập nhật (nếu có) chỉ xảy ra sau khi Member A đóng và mở lại popup (hoặc reload trang).

## 7. Giả Thuyết Nguyên Nhân (Cần Dev Xác Nhận)

- Popup chỉ fetch dữ liệu **một lần khi mở** (on-mount), không subscribe vào event real-time (WebSocket/Socket.IO) để nhận cập nhật vote mới trong lúc đang mở.
- Nếu có subscribe real-time nhưng state của 2 tab ("Not voted" / "Answers") không được cập nhật đồng thời trong cùng 1 event handler → có thể góp phần gây ra tình trạng "danh sách chưa vote hiển thị sai khi empty" đã ghi nhận ở BUG-003 (ví dụ: xóa khỏi "Not voted" nhưng không refresh render, dẫn đến empty state không đúng).
- Đây có khả năng là **root cause chung** cho cả BUG-002 và BUG-003 — cùng nằm ở lớp real-time sync của tính năng Poll.

## 8. Ảnh Hưởng (Impact)

Người xem popup (đặc biệt là người tạo poll/Owner đang theo dõi) không nắm được tiến độ vote theo thời gian thực, phải tự thao tác đóng/mở lại để thấy dữ liệu mới → trải nghiệm kém và dễ gây hiểu nhầm poll đã kết thúc/hết người vote hay chưa.

## 9. Thông Tin Cần Bổ Sung (Ambiguities)

| # | Câu hỏi | Mức độ |
|---|---|---|
| AMB-01 | Sau khi đóng popup và mở lại, dữ liệu có hiển thị đúng không (chỉ thiếu real-time), hay vẫn sai kể cả sau khi mở lại? | 🔴 High |
| AMB-02 | Bug xảy ra với **mọi** vote, hay chỉ khi vote diễn ra dồn dập/liên tục (liên quan BUG-002)? | 🔴 High |
| AMB-03 | Có screenshot/video minh họa không? | 🟡 Medium |
| AMB-04 | Vấn đề có xảy ra ở cả 2 chiều không (vote → unvote cũng không re-render tab "Not voted")? | 🟡 Medium |

## 10. Đề Xuất Kiểm Thử Bổ Sung (Regression)

1. Mở popup ở tab "Not voted", để 1 member khác vote → quan sát real-time không đóng popup.
2. Đóng popup và mở lại ngay sau khi member vừa vote → kiểm tra dữ liệu đã đúng chưa (phân biệt bug real-time vs bug data).
3. Test unvote: member đã vote rồi bỏ vote → kiểm tra tab "Answers" → "Not voted" có tự cập nhật không.
4. Kiểm tra WebSocket/network log khi vote xảy ra trong lúc popup đang mở — xác nhận event có được gửi/nhận không.
5. Test với nhiều popup đang mở đồng thời trên nhiều client — kiểm tra tất cả có cùng nhận real-time update không.

## 11. Đính Kèm

*(Chưa có screenshot/video đính kèm cho bug này — khuyến nghị bổ sung để xác định chính xác hành vi lỗi.)*
