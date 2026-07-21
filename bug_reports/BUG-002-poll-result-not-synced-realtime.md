# 🐞 Bug Report: Kết quả bình chọn (Poll) không đồng bộ giữa các thành viên khi vote liên tục

| Trường | Giá trị |
|---|---|
| **Mã bug** | BUG-002 |
| **Module** | Group Chat > Poll (Bình chọn) |
| **Ngày phát hiện** | 21/07/2026 |
| **Người báo cáo** | Nguyễn Thị Diệu Mơ (lethikimchi170499@gmail.com) |
| **Mức độ nghiêm trọng (Severity)** | 🟡 Medium *(tạm xếp — cần xác nhận lại impact thực tế, xem mục 8)* |
| **Mức độ ưu tiên (Priority)** | 🟡 Medium |
| **Trạng thái** | Open |

---

## 1. Mô Tả Tóm Tắt

Khi nhiều thành viên trong nhóm liên tục thay đổi lựa chọn bình chọn (vote/unvote hoặc đổi option) trong cùng một Poll, **kết quả bình chọn hiển thị không đồng bộ (real-time sync)** giữa các thành viên — số vote/số phiếu hiển thị bị lệch giữa client này với client khác tại cùng một thời điểm.

## 2. Môi Trường

- **Ứng dụng:** Chat app (group chat), tính năng Poll (đa lựa chọn — "Multiple choice", "Public voters")
- **Nhóm test:** "VF ăn trưa" — poll "test poll 1" / "test poll 2" (6 members)

## 3. Điều Kiện Tiên Quyết (Preconditions)

- Có sẵn 1 Poll đang active trong nhóm với ≥ 2 lựa chọn (A, B).
- Có ≥ 2 thành viên khác nhau đang mở cùng nhóm chat trên client/thiết bị riêng.

## 4. Các Bước Tái Hiện (Steps to Reproduce) — *cần xác nhận lại với người báo cáo*

1. Member A và Member B cùng mở nhóm chat và cùng xem 1 Poll.
2. Member A vote chọn option A.
3. Member B vote chọn option B, sau đó đổi sang option A, rồi lại đổi sang option B liên tục trong thời gian ngắn.
4. Quan sát số vote / danh sách "Voted: x/6 people" hiển thị trên client của Member A so với client của Member B tại cùng thời điểm.

## 5. Kết Quả Mong Đợi (Expected Result)

Số phiếu bầu và trạng thái "Voted: x/y people" phải được cập nhật **real-time và đồng nhất** trên tất cả client của mọi thành viên, bất kể tần suất thay đổi vote nhanh hay chậm.

## 6. Kết Quả Thực Tế (Actual Result)

Kết quả bình chọn hiển thị **không đồng bộ** giữa các thành viên khi vote bị thay đổi liên tục — có thể xảy ra tình trạng: số vote hiển thị sai lệch, vote cũ không được cập nhật/xóa đúng, hoặc client bị "trễ" không nhận update mới nhất.

> ⚠️ Chi tiết "không đồng bộ" cụ thể ra sao (sai số count, delay bao lâu, có tự đồng bộ lại sau khi refresh hay không) **cần được người báo cáo bổ sung** để xác định chính xác root cause.

## 7. Giả Thuyết Nguyên Nhân (Cần Dev Xác Nhận)

- Race condition khi nhiều request vote/unvote được gửi gần như đồng thời — cập nhật cuối cùng ghi đè (last-write-wins) không đúng thứ tự (out-of-order updates).
- Cơ chế real-time (WebSocket/Socket.IO/polling) có thể bị debounce/throttle không hợp lý, dẫn tới bỏ lỡ event cập nhật khi tần suất thay đổi cao.
- Thiếu cơ chế đồng bộ lại (reconciliation) giữa state cache ở client và state thật ở server sau khi có nhiều thay đổi dồn dập.

## 8. Ảnh Hưởng (Impact)

Thành viên trong nhóm nhìn thấy kết quả bình chọn khác nhau tại cùng một thời điểm → gây hiểu nhầm về kết quả thực tế của Poll, đặc biệt ảnh hưởng nếu Poll dùng để ra quyết định (ví dụ chọn món ăn trưa như trong nhóm test).

## 9. Thông Tin Cần Bổ Sung (Ambiguities)

| # | Câu hỏi | Mức độ |
|---|---|---|
| AMB-01 | "Thay đổi kết quả liên tục" nghĩa là 1 member đổi vote nhiều lần liên tiếp, hay nhiều member cùng vote gần như đồng thời? | 🔴 High |
| AMB-02 | Không đồng bộ là sai **số lượng vote hiển thị**, sai **option được chọn**, hay chỉ là **delay/chậm cập nhật** rồi tự đúng lại sau vài giây? | 🔴 High |
| AMB-03 | Có tái hiện được 100% hay chỉ thỉnh thoảng (flaky)? Tần suất tái hiện? | 🟡 Medium |
| AMB-04 | Load lại (F5/reload) trang có làm số liệu đúng trở lại không? | 🟡 Medium |

## 10. Đề Xuất Kiểm Thử Bổ Sung (Regression)

1. 2 thành viên vote 2 option khác nhau cùng lúc → kiểm tra count đúng trên cả 2 client.
2. 1 thành viên đổi vote liên tục (A→B→A→B) trong 2-3 giây → kiểm tra state cuối cùng đồng nhất trên mọi client.
3. Reload trang sau khi nghi ngờ lệch số liệu → kiểm tra có tự đồng bộ đúng lại không.
4. Kiểm tra network/WebSocket log để xác định event nào bị mất hoặc đến sai thứ tự.

## 11. Đính Kèm

*(Chưa có screenshot/video đính kèm cho bug này — khuyến nghị bổ sung để hỗ trợ debug root cause chính xác hơn.)*
