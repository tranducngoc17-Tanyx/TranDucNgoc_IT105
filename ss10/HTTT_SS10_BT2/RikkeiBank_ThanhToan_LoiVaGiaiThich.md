# Bài thực hành: Tìm lỗi & Sửa Sequence Diagram – Chức năng Thanh toán RikkeiBank

## 1. Xác định 2 lỗi trong bản vẽ của thực tập sinh

### ❌ Lỗi 1 — Bước 2: "Kiểm tra định dạng thẻ"

**Vẽ sai:** Thông điệp **Async** gửi sang một Lifeline khác (dựng thêm một đối tượng không có trong kịch bản).

**Vì sao sai:** Đề bài nêu rõ đây là Cổng Thanh Toán **tự kiểm tra trên chính nó**, không có đối tượng nào khác tham gia xử lý này. Vẽ thành một message Async sang lifeline mới là **bịa ra một thành phần hệ thống không tồn tại** — Dev sẽ hiểu nhầm là cần tạo thêm một service/class riêng để xử lý việc kiểm tra định dạng thẻ, gây sai kiến trúc và tốn công phát triển thừa.

**Loại đúng phải dùng:** **Self** (mũi tên vòng cung quay lại chính Lifeline "Cổng Thanh Toán").

---

### ❌ Lỗi 2 — Bước 6: "Gửi email hóa đơn"

**Vẽ sai:** Thông điệp **Sync** (có mũi tên Return đi kèm ngay sau).

**Vì sao sai:** Yêu cầu nghiệp vụ ghi rõ "không được chờ EmailServer phản hồi mới đi tiếp". Nếu vẽ là Sync, activation bar của Cổng Thanh Toán sẽ **bị treo (block)** cho tới khi EmailServer trả lời — đúng như cảnh báo trong bối cảnh dự án: "một lỗi có thể khiến luồng xử lý bị chờ khi dịch vụ email phản hồi chậm". Hậu quả thực tế: nếu EmailServer chậm hoặc lỗi, toàn bộ giao dịch thanh toán của khách hàng sẽ bị treo theo, dù tiền đã trừ thành công.

**Loại đúng phải dùng:** **Async** (mũi tên nét liền đầu hở — gửi rồi đi tiếp ngay, không chờ EmailServer phản hồi).

---

## 2. Các bước còn lại (đã đúng, giữ nguyên)

| Bước | Nội dung | Loại đã vẽ | Nhận xét |
|---|---|---|---|
| 3 | Cổng Thanh Toán → Ngân Hàng Lõi: `processPayment()` | Sync | Đúng — cần chờ kết quả giao dịch mới đi tiếp. |
| 4 | Ngân Hàng Lõi → Cổng Thanh Toán: trả kết quả | Return | Đúng — khép activation bar của lời gọi Sync bước 3. |
| 5 | Cổng Thanh Toán → Khách hàng: hiển thị thành công | Return | Đúng — khép activation bar của lời gọi Sync bước 1. |

## 3. Bảng thông điệp sau khi sửa

| # | Từ | Đến | Thông điệp | Loại |
|---|---|---|---|---|
| 1 | Khách hàng | Cổng Thanh Toán | `nhapTheTinDung()` | Sync |
| 2 | Cổng Thanh Toán | Cổng Thanh Toán (chính nó) | `kiemTraDinhDangThe()` | **Self** *(đã sửa)* |
| 3 | Cổng Thanh Toán | Ngân Hàng Lõi | `processPayment()` | Sync |
| 4 | Ngân Hàng Lõi | Cổng Thanh Toán | Kết quả giao dịch | Return |
| 5 | Cổng Thanh Toán | Khách hàng | Hiển thị thành công | Return |
| 6 | Cổng Thanh Toán | EmailServer | `sendReceiptEmail()` | **Async** *(đã sửa)* |

## 4. File đính kèm

- `RikkeiBank_ThanhToan_SequenceDiagram_DaSua.drawio` — sơ đồ đã sửa hoàn chỉnh, mở và chỉnh sửa được trên draw.io (diagrams.net).
