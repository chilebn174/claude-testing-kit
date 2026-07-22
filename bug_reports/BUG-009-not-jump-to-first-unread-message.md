# 🐞 Bug Report: Vào hội thoại có tin chưa đọc không nhảy đến tin nhắn chưa đọc đầu tiên

| Trường | Giá trị |
|---|---|
| **Mã bug** | BUG-009 |
| **Module** | Web App > Nhắn tin core > Tin chưa đọc, cuộn & điều hướng mention |
| **Ticket tham chiếu** | US-W3-12: *Nhảy đến tin chưa đọc đầu tiên và load around position* — `[Web] Epic 3: Nhắn tin core`, Feature F6 |
| **Ngày phát hiện** | 22/07/2026 |
| **Người báo cáo** | Nguyễn Thị Diệu Mơ (lethikimchi170499@gmail.com) |
| **Mức độ nghiêm trọng (Severity)** | 🔴 High |
| **Mức độ ưu tiên (Priority)** | 🔴 High |
| **Trạng thái** | Open |

---

## 1. Mô Tả Tóm Tắt

Theo yêu cầu **US-W3-12** (PRD "Duhat chat core" — Confluence): *"Với vai trò người dùng mở một hội thoại có nhiều tin chưa đọc, tôi muốn được đưa thẳng tới tin chưa đọc đầu tiên với một vạch phân cách rõ ràng, để đọc tiếp từ đúng chỗ đang dở thay vì phải cuộn tìm."*

Trên thực tế, khi mở cuộc hội thoại **"Hòa ca"** đang có tin nhắn chưa đọc (badge hiển thị "1" ở danh sách chat), hệ thống **không tự động cuộn/nhảy tới vị trí tin nhắn chưa đọc đầu tiên**. Vạch phân cách **"Unread messages"** có xuất hiện đúng vị trí trong luồng tin nhắn, nhưng khung chat lại đang cuộn xuống **dưới** vị trí divider đó (gần cuối hội thoại), khiến user phải tự cuộn lên để thấy được đúng điểm bắt đầu đọc tin chưa đọc.

## 2. Môi Trường

- **Ứng dụng:** Duhat Web App (chat client)
- **Cuộc trò chuyện test:** "Hòa ca" (4 members) — chat list hiển thị badge unread = 1 ("VF Em Mơ AUTO: 6")

## 3. Điều Kiện Tiên Quyết (Preconditions)

- Cuộc hội thoại "Hòa ca" đang có ít nhất 1 tin nhắn chưa đọc.
- Current user chưa từng mở/scroll tới cuối cuộc hội thoại này trong phiên hiện tại (tin nhắn chưa đọc còn tồn đọng).

## 4. Các Bước Tái Hiện (Steps to Reproduce)

1. Từ danh sách chat, quan sát cuộc hội thoại **"Hòa ca"** đang có badge unread (hiển thị "1").
2. Click mở cuộc hội thoại "Hòa ca".
3. Quan sát vị trí cuộn (scroll position) của khung chat ngay sau khi mở — so với vị trí của divider **"Unread messages"**.

## 5. Kết Quả Mong Đợi (Expected Result — theo AC của US-W3-12)

> "Khi người dùng mở một hội thoại có tin chưa đọc, hệ thống cuộn tới đúng vị trí tin chưa đọc đầu tiên" kèm **divider "Tin chưa đọc"** phân cách rõ ràng giữa tin đã đọc và tin chưa đọc, đồng thời **load around position** (tải cả tin phía trên và phía dưới vị trí đó) để cuộn lên/xuống liền mạch.

Cụ thể: ngay khi mở "Hòa ca", khung chat phải **tự động cuộn tới vị trí divider "Unread messages"**, hiển thị nó ở khu vực nhìn thấy được (viewport) ngay lập tức — không cần user tự cuộn.

## 6. Kết Quả Thực Tế (Actual Result)

Khi mở "Hòa ca", khung chat **không cuộn tới vị trí divider "Unread messages"**. Thay vào đó, vị trí cuộn nằm **ở dưới** divider (gần cuối luồng tin nhắn — các tin nhắn số như "3", "4", "5" lúc 08:46), khiến divider "Unread messages" nằm ngoài vùng nhìn thấy phía trên. Đồng thời, một nút nổi (floating badge) hiển thị số "1" ở góc dưới phải khung chat — cho thấy hệ thống **biết** vẫn còn 1 tin chưa đọc/chưa cuộn tới, nhưng **không tự nhảy** tới đó khi mở hội thoại như AC yêu cầu.

## 7. Đối Chiếu Với Acceptance Criteria / Todo (US-W3-12)

| Hạng mục trong ticket | Trạng thái thực tế |
|---|---|
| Todo 1: "Build mở hội thoại có tin chưa đọc → cuộn (jump) tới vị trí tin chưa đọc đầu tiên" | ❌ **Chưa đạt** — không tự jump khi mở hội thoại |
| Todo 2: "Build divider 'Tin chưa đọc' phân cách giữa tin đã đọc và tin chưa đọc" | ✅ Có hiển thị divider "Unread messages" đúng vị trí trong luồng tin nhắn |
| Todo 3: "Build load around position: tải cả tin phía trên và phía dưới vị trí đó để cuộn lên/xuống liền mạch" | ⚠️ Cần kiểm tra thêm — chưa xác nhận được vì chưa jump tới đúng vị trí (xem AMB-02) |
| Todo 4: Virtualization cho performance | Không trong phạm vi bug này |

## 8. Giả Thuyết Nguyên Nhân (Cần Dev Xác Nhận)

- Logic tính toán vị trí tin chưa đọc đầu tiên (để đặt divider) hoạt động đúng, nhưng **hành động scroll-to-position sau khi mount component chat chưa được trigger** (ví dụ thiếu gọi `scrollIntoView`/`scrollTo` sau khi divider đã được xác định, hoặc bị gọi trước khi DOM/virtualization render xong nên không có tác dụng).
- Có thể tồn tại race condition giữa việc **load tin nhắn** (kể cả cơ chế "load around position") và **thực hiện scroll** — nếu scroll được gọi quá sớm (trước khi đủ data), sau đó bị ghi đè bởi lần render lại.

## 9. Ảnh Hưởng (Impact)

Đây là vi phạm trực tiếp **AC chính** của US-W3-12 — một tính năng cốt lõi (Feature F6) nhằm giúp user "đọc tiếp từ đúng chỗ đang dở thay vì phải cuộn tìm". Khi bug xảy ra, user mất hoàn toàn lợi ích của tính năng này, đặc biệt nghiêm trọng với hội thoại dài — user buộc phải tự cuộn lên tìm tin chưa đọc, đúng trải nghiệm mà ticket này được tạo ra để loại bỏ.

## 10. Thông Tin Cần Bổ Sung (Ambiguities)

| # | Câu hỏi | Mức độ |
|---|---|---|
| AMB-01 | Bug có xảy ra với **mọi** hội thoại có tin chưa đọc, hay chỉ khi số lượng tin chưa đọc/tổng tin nhắn vượt một ngưỡng nhất định (ví dụ hội thoại dài cần virtualization)? | 🔴 High |
| AMB-02 | Khi cuộn thủ công lên tới divider "Unread messages", các tin nhắn phía trên/dưới có load đầy đủ và mượt (đúng yêu cầu "load around position") hay bị giật/thiếu data? | 🟡 Medium |
| AMB-03 | Nếu chỉ có **1 tin nhắn chưa đọc duy nhất** (như case "Hòa ca" ở đây) so với **nhiều tin chưa đọc liên tiếp**, hành vi có khác nhau không? | 🟡 Medium |
| AMB-04 | Nút nổi số "1" ở góc dưới phải — đây là nút "jump to unread/latest" độc lập; click vào nó có tự cuộn đúng tới vị trí chưa đọc không (cần test riêng, có thể liên quan BUG-007 về indicator mention không tương thích tương tự)? | 🟢 Low |

## 11. Đề Xuất Kiểm Thử Bổ Sung (Regression)

1. Mở hội thoại có **đúng 1** tin chưa đọc → kiểm tra có tự jump tới divider không.
2. Mở hội thoại có **nhiều** tin chưa đọc liên tiếp → kiểm tra hành vi jump.
3. Mở hội thoại **dài** (nhiều trăm tin nhắn, cần virtualization) có tin chưa đọc ở giữa lịch sử → kiểm tra jump + load around position.
4. Click vào nút nổi (floating badge) ở góc dưới phải sau khi mở hội thoại → kiểm tra có tự cuộn đúng vị trí chưa đọc không.
5. Sau khi (giả sử) jump đúng tới divider, cuộn lên/xuống → kiểm tra tin nhắn phía trên/dưới load liền mạch, không bị trắng màn hình/giật (virtualization + load around position).
6. Test lại sau khi đánh dấu đã đọc (mark as read khi cuộn) → mở lại hội thoại đã đọc hết → kiểm tra không hiển thị divider thừa.

## 12. Đính Kèm

2 screenshot đính kèm trong báo cáo gốc:
1. Trang Confluence PRD — ticket **US-W3-12** (Epic 3, Feature F6) mô tả đầy đủ yêu cầu, Todo và User flow liên quan.
2. Giao diện thực tế hội thoại "Hòa ca" — cho thấy divider "Unread messages" xuất hiện đúng vị trí trong luồng tin nhắn nhưng khung chat đang cuộn xuống dưới vị trí đó (không tự jump tới divider khi mở hội thoại).
