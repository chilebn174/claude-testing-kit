# Bug Report — Delete Confirmation Missing

| Trường | Nội dung |
|---|---|
| **Bug ID** | BUG_DELETE_CONFIRM_002 |
| **Tiêu đề** | "Xóa chỉ với tôi" chưa hiển thị popup xác nhận xóa |
| **Module** | Tính năng xóa tin nhắn (Delete Message — Xóa với tất cả / Xóa chỉ với tôi) |
| **Loại lỗi** | Functional — Thiếu bước xác nhận (missing confirmation) |
| **Mức độ nghiêm trọng (Severity)** | Medium — Có thể khiến user xóa nhầm dữ liệu mà không có bước cảnh báo |
| **Mức độ ưu tiên (Priority)** | Medium |
| **Người báo cáo** | lethikimchi170499@gmail.com |
| **Ngày ghi nhận** | 2026-08-17 |
| **Môi trường** | Chưa xác định (chưa cung cấp URL/ứng dụng cụ thể) |

## Mô tả lỗi

Tính năng xóa tin nhắn có 2 lựa chọn: **"Xóa với tất cả"** và **"Xóa chỉ với tôi"**.

- Với lựa chọn **"Xóa với tất cả"**: hệ thống hiển thị popup **confirm xóa** trước khi thực hiện (đúng hành vi mong đợi).
- Với lựa chọn **"Xóa chỉ với tôi"**: hệ thống **KHÔNG hiển thị popup confirm xóa** — tin nhắn bị xóa ngay lập tức, không có bước xác nhận.

→ Hành vi không nhất quán giữa 2 lựa chọn xóa, tiềm ẩn rủi ro user xóa nhầm tin nhắn phía "chỉ với tôi" mà không có cơ hội hủy thao tác.

## Các bước tái hiện (Steps to Reproduce)

1. Mở một cuộc trò chuyện có tin nhắn đã gửi/nhận.
2. Chọn (long-press / click chuột phải hoặc menu tùy chọn của tin nhắn) → chọn hành động **Xóa**.
3. Từ menu xóa, chọn **"Xóa chỉ với tôi"**.
4. Quan sát: có popup xác nhận xóa hiện ra hay không.
5. So sánh với thao tác chọn **"Xóa với tất cả"** ở cùng tin nhắn (hoặc tin nhắn khác) — bước này có hiển thị confirm.

## Kết quả thực tế (Actual Result)

Khi chọn **"Xóa chỉ với tôi"**, tin nhắn bị xóa ngay, **không có popup confirm xóa** hiển thị.

## Kết quả mong đợi (Expected Result)

Khi chọn **"Xóa chỉ với tôi"**, hệ thống phải hiển thị popup xác nhận xóa (confirm dialog) tương tự như hành vi của **"Xóa với tất cả"**, trước khi thực sự xóa tin nhắn.

## Nguyên nhân nghi ngờ (Suspected Root Cause)

Chưa xác nhận được (chưa có source code/ứng dụng cụ thể để inspect). Khả năng:

- Luồng xử lý "Xóa chỉ với tôi" được implement tắt (bypass) bước hiển thị dialog confirm mà "Xóa với tất cả" đang có, có thể do thiếu sót khi code 2 luồng riêng biệt thay vì dùng chung 1 component confirm.
- Component confirm dialog chỉ được gắn (bind) vào action "Xóa với tất cả", chưa được gắn vào action "Xóa chỉ với tôi".

## Ghi chú

- Đây là **bug log dạng manual** (ghi nhận theo mô tả nghiệp vụ), chưa có URL/repo ứng dụng cụ thể kèm theo nên **chưa thực hiện investigate trên UI/DOM thực tế**.
- Khi có URL hoặc repo chứa source code của tính năng xóa tin nhắn, cần bổ sung: ảnh chụp màn hình/video minh chứng, và inspect DOM thực tế (component confirm dialog) trước khi xác định chính xác vị trí code cần fix.

---

## Nội dung để tạo Issue trên Jira (copy thủ công)

> Copy từng field bên dưới vào form "Create Issue" trên Jira.

**Project:** *(điền project key, VD: PROJ)*

**Issue Type:** Bug

**Summary:**
```
["Xóa chỉ với tôi"] Không hiển thị popup xác nhận xóa trước khi xóa tin nhắn
```

**Priority:** Medium

**Components:** Messaging / Delete Message

**Affects Version/s:** *(điền version hiện tại đang test, nếu có)*

**Labels:** `manual-bug`, `delete-message`, `missing-confirmation`, `ui-consistency`

**Environment:**
```
Chưa xác định — bổ sung: Browser/App version, OS, Device khi có môi trường test cụ thể.
```

**Description:**
```
*Mô tả:*
Tính năng xóa tin nhắn có 2 lựa chọn: "Xóa với tất cả" và "Xóa chỉ với tôi".
"Xóa với tất cả" hiển thị popup confirm trước khi xóa (đúng hành vi mong đợi).
"Xóa chỉ với tôi" KHÔNG hiển thị popup confirm — tin nhắn bị xóa ngay lập tức.
Hành vi không nhất quán giữa 2 lựa chọn, tiềm ẩn rủi ro xóa nhầm.

*Steps to Reproduce:*
1. Mở một cuộc trò chuyện có tin nhắn đã gửi/nhận.
2. Long-press / click chuột phải vào tin nhắn → chọn hành động Xóa.
3. Từ menu xóa, chọn "Xóa chỉ với tôi".
4. Quan sát có popup xác nhận xóa hiện ra hay không.
5. So sánh với thao tác "Xóa với tất cả" (có hiển thị confirm) trên cùng tin nhắn/tin nhắn khác.

*Actual Result:*
Chọn "Xóa chỉ với tôi" → tin nhắn bị xóa ngay, không có popup confirm.

*Expected Result:*
Chọn "Xóa chỉ với tôi" → phải hiển thị popup confirm xóa tương tự "Xóa với tất cả",
trước khi tin nhắn thực sự bị xóa.

*Suspected root cause (chưa xác nhận):*
- Luồng "Xóa chỉ với tôi" bypass bước hiển thị dialog confirm mà "Xóa với tất cả"
  đang có (2 luồng code riêng biệt thay vì dùng chung 1 component confirm).
- Component confirm dialog chỉ được bind vào action "Xóa với tất cả".
```

**Attachments:** *(đính kèm screenshot/video minh chứng khi có môi trường test thực tế)*
