---
name: Manual Testing No Doc
description: Skill sinh manual test cases theo quy trình AI-RBT khi DỰ ÁN CHƯA CÓ tài liệu yêu cầu (không có requirement/user story/Figma) — tự động khám phá (discovery) UI/DOM thực tế qua Playwright MCP để dựng lại requirement, đánh dấu rõ phần verified vs assumption, rồi mới chạy tiếp FULL RBT 6 bước. Tái sử dụng được cho bất kỳ tính năng nào (Login, Đăng ký, Checkout, Quên mật khẩu...).
---

# Manual Testing — Khi Chưa Có Tài Liệu (Discovery + FULL RBT)

## Mục đích

Nhiều dự án thực tế không có requirement document, user story hay Figma — chỉ có URL/app đang chạy.
Skill này chèn thêm **Bước 0: Khám phá UI** trước quy trình FULL RBT 6 bước của skill
`rbt_manual_testing`, để AI **tự dựng lại requirement từ hành vi thực tế của UI** thay vì đoán mò,
sau đó chạy tiếp đúng 6 bước RBT như bình thường.

Skill này **không thay thế** `rbt_manual_testing` — nó là phần mở rộng phía trước, dùng chung
Mode FULL RBT của skill đó cho Bước 1-6.

## Khi nào dùng

- User yêu cầu sinh test case cho 1 tính năng nhưng **không cung cấp** requirement/user story/tài liệu nào
- User nói: "chưa có tài liệu", "app đã chạy rồi nhưng không có spec", "tự inspect rồi viết test case giúp"
- Có URL hoặc môi trường test thật để mở browser kiểm tra

**KHÔNG dùng khi:** User đã có requirement/user story rõ ràng → dùng thẳng `rbt_manual_testing`
(Mode QUICK hoặc FULL RBT) như bình thường, không cần qua Bước 0.

## Quy trình

### Bước 0: Khám phá UI & Dựng lại Requirement (Discovery) — BẮT BUỘC khi không có tài liệu

Tuân thủ nghiêm ngặt **Browser Rules** trong `CLAUDE.md`:
```
navigate → resize(1920×1080) → wait_for(page load) → snapshot → interact → screenshot(on_fail)
```
Headed mode, KHÔNG đoán locator, KHÔNG suy diễn khi chưa inspect thực tế.

**Agent phải:**

1. **Mở browser thật** tới URL của tính năng cần test (hỏi user URL nếu chưa có).
2. **Phân tích giao diện thực tế** — áp dụng đúng kỹ thuật trong skill `requirements_analyzer`:
   - Layout Analysis: Header/Sidebar/Form/Main content
   - Thu thập toàn bộ input field: đọc trực tiếp thuộc tính DOM thật (`type`, `required`,
     `maxlength`, `minlength`, `pattern`) — **không đoán**
   - Thu thập các nút/hành động (Submit, Cancel...) và điều kiện enable/disable
   - Trích xuất workflow: phụ thuộc giữa các thành phần UI
3. **Chủ động thử hành vi biên trên UI thật** để lộ ra rule chưa từng được viết ở đâu:
   - Để trống field bắt buộc → đọc nguyên văn thông báo lỗi thật hiển thị
   - Nhập chuỗi rất dài → xem UI có chặn nhập (maxlength) hay validate sau submit
   - Với rule ẩn khả nghi (VD: khóa tài khoản sau N lần sai) → thử trên **môi trường
     test/staging**, không dùng dữ liệu/tài khoản production, quan sát hành vi thật
   - Đọc thông báo lỗi/success message nguyên văn để dùng làm Expected Result sau này
4. **Tổng hợp thành "Requirement Dựng Lại"** theo đúng cấu trúc Output ở skill
   `requirements_analyzer` (Overview, Functional Requirements, Field Specification table,
   Business Rules & Validations).
5. **Đánh dấu rõ ràng 2 loại thông tin, không được trộn lẫn:**
   - ✅ **Verified (quan sát được từ UI/DOM thật)** — có bằng chứng cụ thể (snapshot, DOM attribute, message thật)
   - ❓ **Assumption (chưa xác nhận được)** — hành vi không thể suy ra chỉ từ UI (VD: thời gian khóa
     tài khoản chính xác bao lâu nếu không tiện thử hết), cần hỏi PO/BA/Dev
6. **Trình bày "Requirement Dựng Lại" cho user và dừng lại chờ xác nhận** trước khi sang Bước 1.

> ⚠️ **Human Checkpoint bắt buộc.** Requirement ở bước này là suy luận từ hành vi UI, không phải
> tài liệu chính thức — nếu chạy tiếp mà chưa xác nhận, rủi ro sai lệch logic sẽ lan sang toàn bộ
> test case ở Bước 5. User (Tester/PO) phải xác nhận hoặc bổ sung trước khi tiếp tục.

### Bước 1–6: Chạy đúng Mode FULL RBT của skill `rbt_manual_testing`

Dùng **"Requirement Dựng Lại"** ở Bước 0 (đã được user xác nhận) làm input cho **Bước 1
(Context & Role-play)**, sau đó thực hiện tuần tự Bước 2 → 6 **y hệt** phần "Mode 2: FULL RBT"
đã định nghĩa trong `.claude/skills/rbt_manual_testing/SKILL.md`, bao gồm:

- Bước 2: Analysis & QnA (Q&A ở đây có thể tái dùng các mục "Assumption" từ Bước 0 làm câu hỏi có sẵn)
- Bước 3: Decomposition
- Bước 4: Traceability & Gap Analysis (Human Checkpoint)
- Bước 5: RBT & TC Generation (áp dụng EP/BVA/Decision Table/State Transition + Field-Level Validation)
- Bước 6: Template Mapping

Không lặp lại nội dung chi tiết ở đây — agent **PHẢI đọc và áp dụng đúng** phần Mode FULL RBT
trong skill `rbt_manual_testing` cho các bước này.

## Anti-Patterns (NGHIÊM CẤM)

- ❌ Bỏ qua Bước 0, tự đoán field/validation/business rule mà không mở browser kiểm tra thật
- ❌ Trộn lẫn thông tin "Verified" và "Assumption" mà không đánh dấu riêng biệt
- ❌ Thử hành vi rủi ro (brute-force thật, spam email/SMS thật, xóa dữ liệu thật) trên môi trường
  **production** — chỉ thực hiện trên môi trường test/staging đã được user xác nhận an toàn
- ❌ Chạy tiếp Bước 1-6 khi user chưa xác nhận "Requirement Dựng Lại" ở Bước 0
- ❌ Copy nguyên văn các Anti-pattern khác đã liệt kê trong skill `rbt_manual_testing` (Mode FULL RBT)

## Output

| Bước | Output |
|------|--------|
| 0 | Requirement Dựng Lại (Field Specification + Business Rules), đánh dấu Verified/Assumption |
| 1-6 | Giống hệt Output của Mode FULL RBT trong skill `rbt_manual_testing` |

Tất cả output bằng **Tiếng Việt**, dùng **Artifact** nếu nội dung dài.
