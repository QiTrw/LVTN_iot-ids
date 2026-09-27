# Tập dữ liệu CICIoT2023

## 1. Nguồn gốc
- **Tên:** CICIoT2023
- **Nhà cung cấp:** Viện An ninh mạng Canada (CIC), Đại học New Brunswick (UNB).
- **Link tải:** [https://www.unb.ca/cic/datasets/iotdataset-2023.html](https://www.unb.ca/cic/datasets/iotdataset-2023.html)
- **Mô tả:** Bộ dữ liệu được thu thập từ 105 thiết bị IoT thật trong một cấu trúc mạng mở rộng.

## 2. Đặc trưng (Features)
- Tập dữ liệu bao gồm 39 đặc trưng được trích xuất trực tiếp từ luồng mạng.
- Các nhóm đặc trưng chính:
  - Thông tin header: `Header_length`, `Protocol Type`, `Time to live`, `Rate`.
  - Cờ TCP: `FIN`, `SYN`, `RST`, `PSH`, `ACK`, `ECE`, `CWR` (và số lượng `_count`).
  - Giao thức tầng ứng dụng: `HTTP`, `HTTPS`, `DNS`, `Telnet`, `SMTP`, `SSH`, `IRC`, `TCP`, `UDP`, `DHCP`, `ARP`, `ICMP`, `IGMP`, `IPv`, `LLC`.
  - Thống kê gói tin: `Tot sum`, `Min`, `Max`, `AVG`, `Std`, `Tot size`, `IAT`, `Number`, `Variance`.

## 3. Nhãn (Labels)
- 34 nhãn gốc được gom thành **8 nhóm nhãn chính** phục vụ huấn luyện:
  - `Benign` (0)
  - `DDoS` (1)
  - `DoS` (2)
  - `BruteForce` (3)
  - `Spoofing` (4)
  - `Recon` (5)
  - `Web-based` (6)
  - `Mirai` (7)

# Quy trình Tiền xử lý Dữ liệu

Quy trình tiền xử lý được mô tả cụ thể trong tài liệu báo cáo của LVTN được tóm tắt như sau:

## 1. Làm sạch dữ liệu
- **Loại bỏ đặc trưng dư thừa:** Loại bỏ cột `Number` và `Tot_sum` vì `Tot_sum = Number * Tot_size` (gây đa cộng tuyến).
- **Xử lý giá trị thiếu (NaN):** Kiểm tra và loại bỏ các dòng có giá trị NaN hoặc trùng lặp.

## 2. Chuẩn hóa đặc trưng
- **Chuẩn hóa 4 đặc trưng bộ đếm cờ:** `ACK_count`, `SYN_count`, `FIN_count`, `RST_count` được chuẩn hóa lại để tránh lệch scale.
- Sử dụng `StandardScaler` cho các đặc trưng số.

## 3. Mã hóa nhãn (Label Encoding)
- Chuyển đổi 34 nhãn gốc thành 8 nhóm nhãn chính (từ 0 đến 7).

## 4. Chia tập dữ liệu (Data Splitting)
- **Train:** 64% (4,468,344 mẫu)
- **Validation:** 16% (1,117,087 mẫu)
- **Test:** 20% (1,396,358 mẫu)

## 5. Cân bằng dữ liệu (Handling Imbalance)
- Áp dụng kỹ thuật **Undersampling** cho các lớp chiếm ưu thế (DDoS, DoS, Mirai) để giảm thiểu mất cân bằng nghiêm trọng giữa các lớp.
