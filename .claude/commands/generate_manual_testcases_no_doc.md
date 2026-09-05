---
description: Sinh manual test cases theo AI-RBT khi dự án CHƯA CÓ tài liệu yêu cầu — tự khám phá UI thật trước, rồi chạy FULL RBT 6 bước.
skills:
  - manual_testing_no_doc
  - requirements_analyzer
  - ui_debug_agent
  - rbt_manual_testing
---

> **BẮT BUỘC (MANDATORY SKILL):** Bạn PHẢI nạp và đọc kỹ nội dung của skill
> **`manual_testing_no_doc`** (tại `.claude/skills/manual_testing_no_doc/SKILL.md`) trước khi
> bắt đầu thực hiện tác vụ này. Tham khảo thêm `requirements_analyzer` (kỹ thuật phân tích UI)
> và `ui_debug_agent` (inspect DOM) cho Bước 0, và `rbt_manual_testing` (Mode FULL RBT) cho Bước 1-6.

# Workflow: Sinh Manual Test Cases khi Chưa Có Tài Liệu (Discovery + FULL RBT)

Workflow này dùng khi user **không cung cấp** requirement/user story/Figma cho tính năng cần
test — chỉ có URL hoặc app đang chạy thật.

## ⚠️ Nguyên tắc thực thi

- Nếu user **đã có** requirement rõ ràng → dừng lại, đề xuất dùng
  `/generate_testcases_from_requirements` (QUICK) hoặc `/generate_manual_testcases_rbt` (FULL RBT)
  thay vì workflow này.
- Nếu user **chưa có URL/môi trường test** để mở browser → hỏi user cung cấp trước khi bắt đầu.
- **BẮT BUỘC chạy tuần tự**: Bước 0 (Discovery) → Bước 1 → ... → Bước 6, không gộp.
- **PHẢI dừng lại** chờ user xác nhận tại: Bước 0 (Requirement Dựng Lại), Bước 2 (Q&A),
  Bước 4 (Review Scenarios).
- Chỉ thử các hành vi biên (sai mật khẩu nhiều lần, spam submit...) trên môi trường
  **test/staging** đã được user xác nhận an toàn — không thao tác trên production.
- Tất cả output bằng **Tiếng Việt**.

## Các bước thực hiện

### Bước 0: Khám phá UI & Dựng lại Requirement (Discovery)
1. Mở browser thật (headed, resize 1920×1080) tới URL tính năng cần test
2. Snapshot DOM, liệt kê toàn bộ field + thuộc tính thật (`type`, `required`, `maxlength`, `pattern`)
3. Thử hành vi biên trên UI thật (bỏ trống field, nhập quá dài, sai nhiều lần...) để lộ ra
   validation/business rule chưa từng được viết
4. Tổng hợp "Requirement Dựng Lại" — đánh dấu rõ ✅ Verified (quan sát được) vs ❓ Assumption
   (cần hỏi PO/BA)
5. **DỪNG LẠI — chờ user xác nhận** Requirement Dựng Lại → sang Bước 1

### Bước 1-6: Thực hiện theo hướng dẫn Mode FULL RBT trong skill `rbt_manual_testing`
Dùng Requirement Dựng Lại ở Bước 0 làm input cho Bước 1 (Context & Role-play), sau đó chạy
tuần tự Bước 2 (Analysis & QnA) → Bước 3 (Decomposition) → Bước 4 (Traceability, Human
Checkpoint) → Bước 5 (RBT & TC Generation, áp dụng EP/BVA/Decision Table/State Transition +
Field-Level Validation) → Bước 6 (Template Mapping).

## Output

- Requirement Dựng Lại (Field Specification + Business Rules, Verified vs Assumption)
- Traceability Matrix
- Bảng Test Cases Markdown hoàn chỉnh, sẵn sàng copy sang Excel/Jira/TestRail
