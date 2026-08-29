# Test Cases: Phân Quyền Pin/UnPin Tin Trong Nhóm (v2)

> **Mode:** QUICK (skill `rbt_manual_testing`)
> **Nguồn:** Bảng phân quyền ghim tin trong nhóm (Role/Action x Member/Admin/Owner)
> **Kỹ thuật áp dụng:** Decision Table (tổ hợp Role x Action x Đối tượng tin) + Negative/Security Testing (bypass quyền qua API) + State Transition (cấp/thu hồi quyền)

## Bảng Phân Quyền Gốc

| Role / Action | Member | Admin | Owner |
|---|---|---|---|
| Pin other member | x (*) | x | x |
| UnPin other member | | x | x |
| Pin Admin Other | x (*) | x | x |
| UnPin Admin Other | | | x |
| Pin Owner | x (*) | x | x |
| UnPin Owner | | | x |

`(*)`: Member chỉ có quyền Pin khi **được cấp quyền (assign permission) trong nhóm**. Quyền cấp riêng cho hành động **Pin**, không áp dụng cho **UnPin** (cột Member ở mọi dòng UnPin đều trống).

## Giả định (Assumptions — cần PO/BA xác nhận)

- **A1:** "Member" trong bảng là thành viên thường (không phải Admin/Owner), chưa được cấp quyền ghim tin, trừ khi ghi rõ "đã được cấp quyền".
- **A2:** Quyền cấp cho Member ("được phân quyền trong nhóm") chỉ mở khoá hành động Pin, không mở khoá UnPin — do bảng gốc không đánh dấu `(*)` ở bất kỳ dòng UnPin nào.
- **A3:** Test case cho trường hợp tự ghim/tự bỏ ghim tin do chính actor đăng (self-pin) chưa được quy định rõ trong bảng gốc — tạm coi các dòng "Pin/UnPin other member", "Pin/UnPin Admin Other" chỉ áp dụng cho tin của **người khác** cùng role, không phải tin của chính actor.
- **A4:** User đã rời nhóm không còn quyền thao tác Pin/UnPin dưới bất kỳ hình thức nào.

Nếu giả định sai lệch với business rule thực tế, cần cập nhật lại test cases tương ứng.

---

## Test Cases

| TC ID | Module | Test Scenario | Pre-Condition | Test Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|---|---|
| GROUP_PIN_TC_001 | Pin/UnPin Permission | Owner ghim tin của Member khác | User đăng nhập với role Owner trong nhóm `GRP_PIN_TEST_01`; nhóm có tin nhắn do Member `member_a` đăng | 1. Mở nhóm `GRP_PIN_TEST_01`<br>2. Chọn tin nhắn của `member_a`<br>3. Nhấn "Pin" | Group: `GRP_PIN_TEST_01`; Actor: Owner `owner_01`; Target message: từ `member_a` | 1. Nút Pin hiển thị và có thể nhấn<br>2. Tin được ghim thành công, xuất hiện ở mục "Tin đã ghim" | High |
| GROUP_PIN_TC_002 | Pin/UnPin Permission | Owner ghim tin của Admin khác | Owner đăng nhập; nhóm có tin do Admin `admin_a` đăng | 1. Mở nhóm<br>2. Chọn tin của `admin_a`<br>3. Nhấn "Pin" | Actor: Owner `owner_01`; Target: tin của `admin_a` | Ghim thành công, tin hiển thị trong danh sách tin đã ghim | High |
| GROUP_PIN_TC_003 | Pin/UnPin Permission | Owner ghim tin do chính mình đăng | Owner đăng nhập; đã đăng 1 tin trong nhóm | 1. Mở nhóm<br>2. Chọn tin do chính Owner đăng<br>3. Nhấn "Pin" | Actor: Owner `owner_01`; Target: tin của chính `owner_01` | Ghim thành công | Medium |
| GROUP_PIN_TC_004 | Pin/UnPin Permission | Owner bỏ ghim tin của Member khác | Tin của `member_a` đang ở trạng thái đã ghim | 1. Mở mục "Tin đã ghim"<br>2. Chọn tin của `member_a`<br>3. Nhấn "UnPin" | Actor: Owner `owner_01`; Target: tin đã ghim của `member_a` | Bỏ ghim thành công, tin biến mất khỏi danh sách đã ghim | High |
| GROUP_PIN_TC_005 | Pin/UnPin Permission | Owner bỏ ghim tin của Admin khác | Tin của `admin_a` đang ở trạng thái đã ghim | 1. Mở mục "Tin đã ghim"<br>2. Chọn tin của `admin_a`<br>3. Nhấn "UnPin" | Actor: Owner `owner_01`; Target: tin đã ghim của `admin_a` | Bỏ ghim thành công | High |
| GROUP_PIN_TC_006 | Pin/UnPin Permission | Owner bỏ ghim tin do chính mình đăng | Tin của Owner đang ở trạng thái đã ghim | 1. Mở mục "Tin đã ghim"<br>2. Chọn tin của chính Owner<br>3. Nhấn "UnPin" | Actor: Owner `owner_01`; Target: tin đã ghim của chính mình | Bỏ ghim thành công | Medium |
| GROUP_PIN_TC_007 | Pin/UnPin Permission | Admin ghim tin của Member khác | User đăng nhập với role Admin; nhóm có tin của `member_a` | 1. Mở nhóm<br>2. Chọn tin của `member_a`<br>3. Nhấn "Pin" | Actor: Admin `admin_01`; Target: tin của `member_a` | Ghim thành công | High |
| GROUP_PIN_TC_008 | Pin/UnPin Permission | Admin ghim tin của Admin khác | Admin đăng nhập; nhóm có tin của Admin khác `admin_b` | 1. Mở nhóm<br>2. Chọn tin của `admin_b`<br>3. Nhấn "Pin" | Actor: Admin `admin_01`; Target: tin của `admin_b` | Ghim thành công | High |
| GROUP_PIN_TC_009 | Pin/UnPin Permission | Admin ghim tin của Owner | Admin đăng nhập; nhóm có tin của Owner | 1. Mở nhóm<br>2. Chọn tin của Owner<br>3. Nhấn "Pin" | Actor: Admin `admin_01`; Target: tin của `owner_01` | Ghim thành công | High |
| GROUP_PIN_TC_010 | Pin/UnPin Permission | Admin bỏ ghim tin của Member khác | Tin của `member_a` đang ở trạng thái đã ghim | 1. Mở mục "Tin đã ghim"<br>2. Chọn tin của `member_a`<br>3. Nhấn "UnPin" | Actor: Admin `admin_01`; Target: tin đã ghim của `member_a` | Bỏ ghim thành công | High |
| GROUP_PIN_TC_011 | Pin/UnPin Permission | [Negative] Admin bỏ ghim tin của Admin khác | Tin của `admin_b` đang ở trạng thái đã ghim | 1. Mở mục "Tin đã ghim"<br>2. Tìm tin của `admin_b` | Actor: Admin `admin_01`; Target: tin đã ghim của `admin_b` | Nút "UnPin" không hiển thị hoặc bị disable với tin của Admin khác; Admin không thể bỏ ghim | High |
| GROUP_PIN_TC_012 | Pin/UnPin Permission | [Negative] Admin bỏ ghim tin của Owner | Tin của Owner đang ở trạng thái đã ghim | 1. Mở mục "Tin đã ghim"<br>2. Tìm tin của Owner | Actor: Admin `admin_01`; Target: tin đã ghim của `owner_01` | Nút "UnPin" không hiển thị hoặc bị disable với tin của Owner; Admin không thể bỏ ghim | High |
| GROUP_PIN_TC_013 | Pin/UnPin Permission | [Negative] Member (chưa được cấp quyền) cố ghim tin của Member khác | User đăng nhập role Member `member_b`, **chưa** được cấp quyền ghim tin; nhóm có tin của `member_a` | 1. Mở nhóm<br>2. Chọn tin của `member_a` | Actor: Member `member_b` (no permission); Target: tin của `member_a` | Nút "Pin" không hiển thị với `member_b`; nếu gọi thao tác vẫn bị chặn | High |
| GROUP_PIN_TC_014 | Pin/UnPin Permission | [Negative] Member (chưa được cấp quyền) cố ghim tin của Admin khác | Member `member_b` chưa được cấp quyền; nhóm có tin của `admin_a` | 1. Mở nhóm<br>2. Chọn tin của `admin_a` | Actor: Member `member_b` (no permission); Target: tin của `admin_a` | Nút "Pin" không hiển thị/không thao tác được | High |
| GROUP_PIN_TC_015 | Pin/UnPin Permission | [Negative] Member (chưa được cấp quyền) cố ghim tin của Owner | Member `member_b` chưa được cấp quyền; nhóm có tin của Owner | 1. Mở nhóm<br>2. Chọn tin của Owner | Actor: Member `member_b` (no permission); Target: tin của `owner_01` | Nút "Pin" không hiển thị/không thao tác được | High |
| GROUP_PIN_TC_016 | Pin/UnPin Permission | [Negative] Member (chưa được cấp quyền) cố bỏ ghim tin của Member khác | Tin của `member_a` đang ở trạng thái đã ghim; `member_b` chưa được cấp quyền | 1. Mở mục "Tin đã ghim"<br>2. Tìm tin của `member_a` | Actor: Member `member_b`; Target: tin đã ghim của `member_a` | Nút "UnPin" không hiển thị với Member | High |
| GROUP_PIN_TC_017 | Pin/UnPin Permission | [Negative] Member cố bỏ ghim tin của Admin khác | Tin của `admin_a` đang ở trạng thái đã ghim | 1. Mở mục "Tin đã ghim"<br>2. Tìm tin của `admin_a` | Actor: Member `member_b`; Target: tin đã ghim của `admin_a` | Nút "UnPin" không hiển thị với Member | Medium |
| GROUP_PIN_TC_018 | Pin/UnPin Permission | [Negative] Member cố bỏ ghim tin của Owner | Tin của Owner đang ở trạng thái đã ghim | 1. Mở mục "Tin đã ghim"<br>2. Tìm tin của Owner | Actor: Member `member_b`; Target: tin đã ghim của `owner_01` | Nút "UnPin" không hiển thị với Member | Medium |
| GROUP_PIN_TC_019 | Pin/UnPin Permission | Member đã được cấp quyền ghim tin — ghim tin của Member khác | Owner/Admin đã cấp quyền "Pin tin" cho `member_c`; nhóm có tin của `member_a` | 1. Đăng nhập bằng `member_c`<br>2. Mở nhóm<br>3. Chọn tin của `member_a`<br>4. Nhấn "Pin" | Actor: Member `member_c` (đã cấp quyền Pin); Target: tin của `member_a` | Nút "Pin" hiển thị; ghim thành công | High |
| GROUP_PIN_TC_020 | Pin/UnPin Permission | Member đã được cấp quyền ghim tin — ghim tin của Admin khác | `member_c` đã được cấp quyền Pin; nhóm có tin của `admin_a` | 1. Đăng nhập `member_c`<br>2. Chọn tin của `admin_a`<br>3. Nhấn "Pin" | Actor: Member `member_c` (đã cấp quyền); Target: tin của `admin_a` | Ghim thành công | High |
| GROUP_PIN_TC_021 | Pin/UnPin Permission | Member đã được cấp quyền ghim tin — ghim tin của Owner | `member_c` đã được cấp quyền Pin; nhóm có tin của Owner | 1. Đăng nhập `member_c`<br>2. Chọn tin của Owner<br>3. Nhấn "Pin" | Actor: Member `member_c` (đã cấp quyền); Target: tin của `owner_01` | Ghim thành công | High |
| GROUP_PIN_TC_022 | Pin/UnPin Permission | [Negative] Member đã được cấp quyền Pin nhưng cố bỏ ghim tin của Member khác | `member_c` đã được cấp quyền Pin (không phải UnPin); tin của `member_a` đang ở trạng thái đã ghim | 1. Đăng nhập `member_c`<br>2. Mở mục "Tin đã ghim"<br>3. Tìm tin của `member_a` | Actor: Member `member_c` (chỉ có quyền Pin); Target: tin đã ghim của `member_a` | Nút "UnPin" không hiển thị/không thao tác được — quyền được cấp không mở khoá UnPin | High |
| GROUP_PIN_TC_023 | Pin/UnPin Permission | [Negative] Member đã được cấp quyền Pin nhưng cố bỏ ghim tin của Admin khác | `member_c` đã được cấp quyền Pin; tin của `admin_a` đang ở trạng thái đã ghim | 1. Đăng nhập `member_c`<br>2. Mở mục "Tin đã ghim"<br>3. Tìm tin của `admin_a` | Actor: Member `member_c`; Target: tin đã ghim của `admin_a` | Nút "UnPin" không hiển thị/không thao tác được | Medium |
| GROUP_PIN_TC_024 | Pin/UnPin Permission | [Negative] Member đã được cấp quyền Pin nhưng cố bỏ ghim tin của Owner | `member_c` đã được cấp quyền Pin; tin của Owner đang ở trạng thái đã ghim | 1. Đăng nhập `member_c`<br>2. Mở mục "Tin đã ghim"<br>3. Tìm tin của Owner | Actor: Member `member_c`; Target: tin đã ghim của `owner_01` | Nút "UnPin" không hiển thị/không thao tác được | Medium |
| GROUP_PIN_TC_025 | Pin/UnPin Permission | [State Transition] Owner/Admin cấp quyền Pin cho Member — quyền có hiệu lực ngay | `member_d` hiện chưa có quyền Pin | 1. Owner mở "Cài đặt phân quyền nhóm"<br>2. Bật quyền "Ghim tin" cho `member_d`<br>3. Đăng nhập lại/refresh bằng `member_d`<br>4. Thử ghim 1 tin bất kỳ | Actor cấp quyền: Owner `owner_01`; Target user: `member_d` | Sau khi được cấp quyền, `member_d` thấy nút "Pin" xuất hiện và ghim thành công | High |
| GROUP_PIN_TC_026 | Pin/UnPin Permission | [State Transition] Owner/Admin thu hồi quyền Pin đã cấp cho Member | `member_d` đang có quyền Pin (đã cấp ở TC_025) | 1. Owner mở "Cài đặt phân quyền nhóm"<br>2. Tắt quyền "Ghim tin" của `member_d`<br>3. Đăng nhập lại/refresh bằng `member_d`<br>4. Thử ghim 1 tin bất kỳ | Actor thu hồi: Owner `owner_01`; Target user: `member_d` | Nút "Pin" không còn hiển thị với `member_d`; thao tác ghim bị từ chối | High |
| GROUP_PIN_TC_027 | Pin/UnPin Permission | [UI Validation] Kiểm tra hiển thị nút Pin/UnPin đúng theo từng role trên UI | Nhóm có đủ 4 loại actor: Member (không quyền), Member (có quyền Pin), Admin, Owner; có tin của Member/Admin/Owner | 1. Lần lượt đăng nhập từng actor<br>2. Mở danh sách tin nhắn và mục "Tin đã ghim"<br>3. Quan sát nút Pin/UnPin trên từng tin | 4 tài khoản actor tương ứng 4 role/trạng thái quyền | Nút Pin/UnPin hiển thị/ẩn đúng khớp 100% với Bảng Phân Quyền Gốc cho từng tổ hợp role x target | Critical |
| GROUP_PIN_TC_028 | Pin/UnPin Permission | [Security] Member không có quyền gọi trực tiếp API UnPin (bỏ qua UI) | `member_b` không có quyền UnPin; tin của `member_a` đang ở trạng thái đã ghim; có access token hợp lệ của `member_b` | 1. Dùng công cụ gọi API (Postman/cURL) gửi request `POST /api/group/{groupId}/messages/{messageId}/unpin` với token của `member_b` | Actor: Member `member_b`; Target message ID của `member_a` đã ghim | API trả về lỗi `403 Forbidden`; tin vẫn còn ở trạng thái đã ghim (không bị bỏ ghim) | Critical |
| GROUP_PIN_TC_029 | Pin/UnPin Permission | [Security] Admin gọi trực tiếp API UnPin tin của Owner (bỏ qua UI ẩn nút) | Admin không có quyền UnPin tin Owner theo bảng; tin của Owner đang ở trạng thái đã ghim | 1. Dùng access token của `admin_01` gọi `POST /api/group/{groupId}/messages/{messageId}/unpin` với messageId của Owner | Actor: Admin `admin_01`; Target: tin đã ghim của `owner_01` | API trả về lỗi `403 Forbidden`; tin vẫn ở trạng thái đã ghim | Critical |
| GROUP_PIN_TC_030 | Pin/UnPin Permission | [Security] Member đã được cấp quyền Pin gọi trực tiếp API UnPin (bỏ qua UI) | `member_c` chỉ có quyền Pin (không có UnPin); tin của `member_a` đang ở trạng thái đã ghim | 1. Dùng access token của `member_c` gọi `POST /api/group/{groupId}/messages/{messageId}/unpin` | Actor: Member `member_c` (chỉ quyền Pin); Target: tin đã ghim của `member_a` | API trả về lỗi `403 Forbidden` — quyền Pin không mở rộng sang UnPin ở tầng server | Critical |
| GROUP_PIN_TC_031 | Pin/UnPin Permission | [Negative] User đã rời nhóm cố gọi API Pin/UnPin | `member_e` vừa rời khỏi nhóm `GRP_PIN_TEST_01`; nhóm có tin đang ở trạng thái chưa ghim và đã ghim | 1. Dùng access token cũ (trước khi rời nhóm) của `member_e` gọi `POST /api/group/{groupId}/messages/{messageId}/pin`<br>2. Lặp lại với endpoint `unpin` | Actor: `member_e` (đã rời nhóm); Target: tin bất kỳ trong nhóm | Cả 2 API đều trả về lỗi `403 Forbidden` (hoặc `401`/`404` tuỳ thiết kế), không thực hiện được thao tác | High |

---

## Tóm Tắt

- **Tổng số Test Cases:** 31
- **Phân bổ theo kỹ thuật:** Decision Table (24 TCs — TC_001 → TC_024), State Transition (2 TCs — TC_025, TC_026), UI Validation (1 TC — TC_027), Security/API Bypass (3 TCs — TC_028 → TC_030), Negative/Boundary quyền truy cập (1 TC — TC_031)
- **Phân bổ theo Priority:** Critical: 4, High: 20, Medium: 7
- **Known limitation:** Chưa có xác nhận business rule cho các giả định A1–A4 ở trên (đặc biệt là hành vi self-pin/self-unpin tin do chính actor đăng, và việc quyền Pin có tự động thu hồi khi user rời/join lại nhóm hay không) — cần PO/BA xác nhận trước khi coi bộ TC là đầy đủ 100%.
