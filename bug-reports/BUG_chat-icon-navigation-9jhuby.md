# Bug Report: Click icon chat không điều hướng sang màn hình chat sau khi nhập PIN thành công

> File này được soạn theo định dạng chuẩn để copy trực tiếp sang Jira (Summary / Description / Steps to Reproduce / Expected / Actual...).
> Các mục đánh dấu `[TBD - cần bổ sung]` là thông tin chưa có, vui lòng điền trước khi tạo issue trên Jira để đảm bảo bug có đầy đủ căn cứ tái hiện lỗi.

---

## Summary

Sau khi nhập mã PIN thành công, click vào icon chat không điều hướng sang màn hình chat.

## Issue Type

Bug

## Priority / Severity

`[TBD - cần bổ sung]` — Đề xuất tạm: **High** (chặn luồng truy cập tính năng chat sau khi xác thực PIN thành công).

## Component / Module

Chat / Navigation (sau màn hình xác thực PIN)

## Environment

| Thông tin | Giá trị |
|---|---|
| Platform | `[TBD - Web / Mobile iOS / Mobile Android]` |
| App version / Build | `[TBD]` |
| Device / Browser | `[TBD]` |
| OS version | `[TBD]` |
| Account / User test | `[TBD]` |
| Ngày phát hiện | 2026-09-07 |

## Preconditions

- User đã có tài khoản hợp lệ và đã thiết lập mã PIN.
- User đang ở màn hình yêu cầu nhập mã PIN (App lock / xác thực PIN).

## Steps to Reproduce

1. Mở app, đi tới màn hình nhập mã PIN.
2. Nhập mã PIN hợp lệ của tài khoản.
3. Xác nhận PIN được chấp nhận (hiển thị thông báo / chuyển màn hình thành công).
4. Click vào icon chat trên màn hình hiện tại (`[TBD - vị trí icon chat: bottom navigation bar / header / home screen...]`).

## Expected Result

Sau khi click icon chat, app phải điều hướng sang màn hình Chat.

## Actual Result

Click vào icon chat **không có phản ứng điều hướng** — người dùng vẫn ở nguyên màn hình hiện tại, không được chuyển sang màn hình chat.

## Attachments / Evidence

- Screenshot / video: `[TBD - đính kèm khi log lên Jira]`
- Console log / network log (nếu có lỗi ẩn phía dưới): `[TBD]`

## Additional Notes

- Chưa xác định được: lỗi xảy ra ở **mọi lần** click hay chỉ **lần đầu tiên** sau khi nhập PIN thành công (có thể liên quan đến state điều hướng chưa được cập nhật ngay sau khi PIN xác thực).
- Cần kiểm tra thêm: có báo lỗi console/log nào không, hay hoàn toàn im lặng (silent fail).
- Repo `claude-testing-kit` hiện chỉ chứa automation framework/skills, không có source code ứng dụng thực tế nên chưa thể xác định root cause qua code — cần trỏ đúng repo ứng dụng (nơi có icon chat/màn hình PIN) để điều tra kỹ hơn nếu cần fix code.
