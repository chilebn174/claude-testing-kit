# Bug Report — Vote Percent Change

| Trường | Nội dung |
|---|---|
| **Bug ID** | BUG_VOTE_PERCENT_001 |
| **Tiêu đề** | Khi chuyển đáp án, % bình chọn của đáp án khác thay đổi |
| **Module** | Tính năng bình chọn (Voting/Poll) |
| **Loại lỗi** | Functional — Logic tính toán |
| **Mức độ nghiêm trọng (Severity)** | High — Sai lệch dữ liệu hiển thị cho toàn bộ user xem kết quả bình chọn |
| **Mức độ ưu tiên (Priority)** | High |
| **Người báo cáo** | lethikimchi170499@gmail.com |
| **Ngày ghi nhận** | 2026-08-15 |
| **Môi trường** | Chưa xác định (chưa cung cấp URL/ứng dụng cụ thể) |

## Mô tả lỗi

Khi người dùng đã bình chọn một đáp án, sau đó **chuyển sang chọn đáp án khác** (đổi từ đáp án A sang đáp án B) trong cùng một câu hỏi bình chọn, thì **% bình chọn của các đáp án KHÁC** — tức các đáp án không phải đáp án cũ (A) và không phải đáp án mới (B) — cũng bị thay đổi theo, dù số lượt bình chọn thực tế của các đáp án đó không hề đổi.

## Các bước tái hiện (Steps to Reproduce)

1. Vào một câu hỏi bình chọn có từ 3 đáp án trở lên (ví dụ: A, B, C, D), mỗi đáp án đã có sẵn số lượt bình chọn khác nhau.
2. Người dùng chọn đáp án **A**.
3. Ghi nhận % hiển thị hiện tại của từng đáp án A, B, C, D.
4. Người dùng đổi lựa chọn từ đáp án A sang đáp án **B** (không tương tác gì với C, D).
5. So sánh % hiển thị mới của C và D với % đã ghi nhận ở bước 3.

## Kết quả thực tế (Actual Result)

% bình chọn của đáp án **C** và **D** (không liên quan đến lượt đổi đáp án) bị thay đổi sau khi user chuyển từ A sang B.

## Kết quả mong đợi (Expected Result)

**Giữ nguyên % bình chọn của các đáp án khác** nếu đáp án đó **không phải là đáp án cũ (trước khi đổi) hoặc đáp án mới (sau khi đổi)** của user. Chỉ % của đáp án cũ (A, bị trừ 1 lượt) và đáp án mới (B, được cộng 1 lượt) mới được phép thay đổi.

## Nguyên nhân nghi ngờ (Suspected Root Cause)

Chưa xác nhận được (chưa có source code/ứng dụng cụ thể để inspect). Một số khả năng cần đội dev kiểm tra:

- Tổng số lượt bình chọn (denominator dùng để tính %) bị tính sai tạm thời tại thời điểm chuyển đổi (ví dụ: cộng vote mới trước khi trừ vote cũ, hoặc ngược lại, khiến tổng bị lệch trong lúc tính toán).
- Toàn bộ danh sách % bị tính lại và làm tròn (rounding) lại từ đầu sau mỗi lần thay đổi, khiến các đáp án không liên quan cũng bị lệch % do sai số làm tròn.
- Công thức tính `percent(option) = count(option) / total_votes * 100` không tách biệt rõ giữa "cộng vote mới" và "trừ vote cũ" thành 2 thao tác atomic.

## Ghi chú

- Đây là **bug log dạng manual** (ghi nhận theo mô tả nghiệp vụ), chưa có URL/repo ứng dụng cụ thể kèm theo nên **chưa thực hiện investigate trên UI/DOM thực tế** theo quy tắc bắt buộc của dự án (`playwright_rules.md` — không đoán locator, phải inspect thực tế).
- Khi có URL hoặc repo chứa source code của tính năng bình chọn, cần bổ sung: ảnh chụp màn hình/video minh chứng, log console/network, và inspect DOM thực tế trước khi viết automation test hoặc xác định chính xác vị trí code cần fix.

---

## Nội dung để tạo Issue trên Jira (copy thủ công)

> Copy từng field bên dưới vào form "Create Issue" trên Jira.

**Project:** *(điền project key, VD: PROJ)*

**Issue Type:** Bug

**Summary:**
```
[Voting] % bình chọn của đáp án khác bị thay đổi khi user chuyển đáp án
```

**Priority:** High

**Components:** Voting / Poll

**Affects Version/s:** *(điền version hiện tại đang test, nếu có)*

**Labels:** `manual-bug`, `voting`, `calculation`, `ui-display`

**Environment:**
```
Chưa xác định — bổ sung: Browser/App version, OS, Device khi có môi trường test cụ thể.
```

**Description:**
```
*Mô tả:*
Khi user đã bình chọn 1 đáp án, sau đó đổi sang đáp án khác trong cùng câu hỏi,
% bình chọn hiển thị của các đáp án KHÔNG liên quan (không phải đáp án cũ/mới)
bị thay đổi sai, dù số lượt bình chọn thực tế của các đáp án đó không đổi.

*Steps to Reproduce:*
1. Vào câu hỏi bình chọn có >= 3 đáp án (A, B, C, D), mỗi đáp án đã có vote khác nhau.
2. Chọn đáp án A. Ghi nhận % hiển thị của A, B, C, D.
3. Đổi lựa chọn từ A sang B (không đụng tới C, D).
4. So sánh % của C, D trước và sau khi đổi.

*Actual Result:*
% của C và D (không liên quan đến lượt đổi) bị thay đổi.

*Expected Result:*
Chỉ % của đáp án cũ (A) và đáp án mới (B) được cập nhật lại. % các đáp án
khác (C, D...) phải giữ nguyên.

*Suspected root cause (chưa xác nhận):*
- Tổng số vote (denominator) bị tính sai tạm thời khi chuyển đổi (cộng vote
  mới trước khi trừ vote cũ, hoặc ngược lại).
- Toàn bộ % bị tính lại + làm tròn (rounding) từ đầu sau mỗi lần đổi, khiến
  các đáp án không liên quan cũng lệch % do sai số làm tròn.
```

**Attachments:** *(đính kèm screenshot/video minh chứng khi có môi trường test thực tế)*
