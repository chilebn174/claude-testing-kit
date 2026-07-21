# 🐞 Bug Report: Danh sách "Chưa bình chọn" hiển thị sai khi ở trạng thái empty (Empty State)

| Trường | Giá trị |
|---|---|
| **Mã bug** | BUG-003 |
| **Module** | Group Chat > Poll (Bình chọn) > Danh sách "Chưa bình chọn" |
| **Ngày phát hiện** | 21/07/2026 |
| **Người báo cáo** | Nguyễn Thị Diệu Mơ (lethikimchi170499@gmail.com) |
| **Mức độ nghiêm trọng (Severity)** | 🟢 Low *(tạm xếp — UI/display issue, cần xác nhận lại impact)* |
| **Mức độ ưu tiên (Priority)** | 🟡 Medium |
| **Trạng thái** | Open |

---

## 1. Mô Tả Tóm Tắt

Danh sách **"Chưa bình chọn"** (danh sách thành viên chưa vote) trong tính năng Poll hiển thị **không đúng** khi rơi vào trạng thái **empty (rỗng)** — tức khi tất cả thành viên đã vote hết (không còn ai "chưa bình chọn"), giao diện của danh sách này không hiển thị đúng như kỳ vọng cho trạng thái rỗng.

## 2. Môi Trường

- **Ứng dụng:** Chat app (group chat), tính năng Poll — mục xem danh sách "Đã bình chọn" / "Chưa bình chọn"
- **Nhóm test:** "VF ăn trưa" — poll "test poll 1" / "test poll 2"

## 3. Điều Kiện Tiên Quyết (Preconditions) — *nháp, cần xác nhận*

- Có 1 Poll đang active trong nhóm.
- Tất cả thành viên trong nhóm đã vote (0 thành viên còn lại ở trạng thái "chưa bình chọn"), **hoặc** poll chưa có ai vote (trường hợp ngược lại — cần làm rõ, xem mục 7).

## 4. Các Bước Tái Hiện (Steps to Reproduce) — *nháp, cần xác nhận lại với người báo cáo*

1. Mở nhóm chat có Poll đang active.
2. Cho tất cả thành viên vote hết (0 người còn "chưa bình chọn").
3. Mở tab/danh sách **"Chưa bình chọn"** của Poll (ví dụ qua link "Voted: x/y people" hoặc chi tiết poll).
4. Quan sát giao diện hiển thị khi danh sách này rỗng.

## 5. Kết Quả Mong Đợi (Expected Result)

Khi danh sách "Chưa bình chọn" rỗng (0 người), giao diện phải hiển thị **empty state rõ ràng** — ví dụ thông báo "Không còn ai chưa bình chọn" / "Tất cả đã bình chọn" hoặc icon/illustration empty state phù hợp, **không** để trống trắng, không lỗi layout, không hiển thị nhầm dữ liệu cũ.

## 6. Kết Quả Thực Tế (Actual Result)

Giao diện hiển thị **chưa đúng** ở trạng thái empty — cụ thể sai như thế nào (trống trắng không có thông báo, hiển thị nhầm danh sách "Đã bình chọn", layout bị vỡ, hiển thị loading vô hạn, hay hiện sai số đếm...) **cần người báo cáo bổ sung** để xác định chính xác (xem mục 7).

## 7. Thông Tin Cần Bổ Sung (Ambiguities)

| # | Câu hỏi | Mức độ |
|---|---|---|
| AMB-01 | "Empty" ở đây là trường hợp **tất cả đã vote** (danh sách chưa vote = 0), hay trường hợp **chưa ai vote** (danh sách đã vote = 0)? | 🔴 High |
| AMB-02 | "Hiển thị chưa đúng" cụ thể là gì: trống trắng không có nội dung, sai text, sai số đếm, hiển thị nhầm danh sách khác, hay lỗi layout/vỡ giao diện? | 🔴 High |
| AMB-03 | Có screenshot/video minh họa trạng thái lỗi này không? | 🟡 Medium |
| AMB-04 | Lỗi xảy ra ngay khi mở lần đầu hay chỉ sau khi danh sách chuyển từ có data → rỗng (real-time, ví dụ người cuối cùng vừa vote xong)? | 🟡 Medium |

## 8. Giả Thuyết Nguyên Nhân (Cần Dev Xác Nhận)

- Component danh sách chưa xử lý case `length === 0` → không có empty state UI riêng, dẫn tới hiển thị trống hoặc giữ lại state cũ.
- Có thể danh sách không tự re-render khi số lượng "chưa vote" chuyển về 0 theo real-time (liên quan tiềm ẩn đến BUG-002 — vấn đề đồng bộ real-time của Poll).

## 9. Đề Xuất Kiểm Thử Bổ Sung (Regression)

1. Mở danh sách "Chưa bình chọn" khi còn 1 người chưa vote → sau đó người đó vote → kiểm tra danh sách có tự cập nhật về empty state đúng không (real-time).
2. Mở lại danh sách "Chưa bình chọn" sau khi tất cả đã vote (reload trang) → kiểm tra empty state hiển thị đúng ngay từ đầu.
3. Kiểm tra danh sách "Đã bình chọn" ở chiều ngược lại (khi chưa ai vote) có empty state đúng không — để xác định bug có ở cả 2 danh sách hay chỉ 1.
4. Test trên poll có "Public voters" bật/tắt — kiểm tra empty state có khác nhau giữa 2 case.

## 10. Đính Kèm

*(Chưa có screenshot/video đính kèm cho bug này — khuyến nghị bổ sung để xác định chính xác lỗi hiển thị.)*
