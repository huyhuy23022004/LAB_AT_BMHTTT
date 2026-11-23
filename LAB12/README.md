# LAB 12 (LAB 3): MÃ HÓA KHÓA CÔNG KHAI

* **Họ và tên:** Bùi Ngọc Huy
* **MSSV:** 1150080137
* **Lớp:** 11_ĐH_CNPM2
* **Môn học:** An toàn và Bảo mật Hệ thống thông tin
* **Bài thực hành:** **LAB 12** – Mã hóa khóa công khai (tương ứng nội dung Lab 3)
* **Video Demo (Lab 12):** [https://youtu.be/ngsLdSlkpJ0](https://youtu.be/ngsLdSlkpJ0)

## 1. Mục tiêu bài thực hành (Lab 12)

* Nắm vững nguyên lý mật mã khóa công khai (cặp khóa public/private) và vận dụng cài đặt bằng **Java (Swing GUI – Maven)** với thư viện chuẩn `java.security` và `javax.crypto`.
* Triển khai và kiểm thử các kỹ thuật: **RSA (OAEP-SHA256)**, **ECC (ECIES trên đường cong secp256r1)**, **Hệ mã lai RSA + AES-256-GCM** cho văn bản dài và **Chữ ký số ECDSA (`SHA256withECDSA`)**.
* Kết nối **Microsoft SQL Server** qua JDBC (`mssql-jdbc`) để ghi nhận, tra cứu và thống kê lịch sử thao tác (`ENCRYPT`, `DECRYPT`, `SIGN`, `VERIFY`) trong bảng `dbo.History`.

## 2. Cấu trúc thư mục dự án

```text
LAB12_1150080137_BuiNgocHuy/
├── LAB3_MaHoaKhoaCongKhai/                 # Dự án Java Maven (NetBeans IDE)
│   ├── pom.xml                             # Khai báo thư viện mssql-jdbc & JDK 17
│   └── src/main/java/week_03/
│       ├── CryptoSupport.java              # Tiện ích Base64, UTF-8, Random, AES-GCM, HKDF, tệp khóa
│       ├── FileTextIO.java                 # Đọc / ghi tệp văn bản (.txt)
│       ├── DbConfig.java                   # Cấu hình kết nối SQL Server (lưu mật khẩu tạm dạng char[])
│       ├── HistoryDao.java                 # Ghi và truy vấn lịch sử bảng dbo.History
│       ├── RSACipher.java                  # Thuật toán RSA-OAEP (SHA-256) hỗ trợ 2048/3072 bit
│       ├── RSADemo.java                    # Bài 3.1: Chương trình Console kiểm thử RSA
│       ├── ECCCipher.java                  # Bài 3.3: Mã hóa ECC (ECDH + HKDF-SHA256 + AES-256-GCM)
│       ├── RSA_AES_Cipher.java             # Bài 3.4: Hệ mã lai RSA-OAEP + AES-256-GCM
│       ├── ECCSignature.java               # Bài 3.5: Chữ ký số ECDSA (SHA256withECDSA)
│       ├── frm_Menu.java/.form             # Giao diện Menu chính
│       ├── frm_KetNoiSQL.java/.form        # Giao diện cấu hình kết nối SQL Server
│       ├── frm_LichSu.java/.form           # Giao diện xem lịch sử dbo.History
│       ├── frm_RSA.java/.form              # Bài 3.2: Giao diện RSA Swing
│       ├── frm_ECC.java/.form              # Bài 3.3: Giao diện ECC + Save/Load Keys
│       ├── frm_RSA_AES.java/.form          # Bài 3.4: Giao diện Hệ mã lai RSA + AES-GCM
│       └── frm_ECC_Signature.java/.form    # Bài 3.5: Giao diện Chữ ký số ECDSA
├── 03_SQL_SERVER/
│   ├── 01_Tao_CSDL.sql                     # Script tạo CSDL LAB3_MaHoaKhoaCongKhai & bảng dbo.History
│   ├── 02_Tao_TaiKhoan_LAB.sql             # Script tạo tài khoản lab3_user (quyền SELECT, INSERT)
│   └── 03_KiemTra_LichSu.sql               # Script kiểm tra TOP 30 dòng và thống kê GROUP BY
├── ANH/                                    # Ảnh chụp màn hình minh chứng chạy thử
└── README.md
```

## 3. Các chức năng đã hoàn thành

1. **Bài 3.1 – RSA (Console – `RSADemo.java`):** Tạo cặp khóa RSA `2048` và `3072` bit, mã hóa/giải mã chuỗi `HELLO RSA` bằng đệm `OAEPWithSHA-256AndMGF1Padding`, đo thời gian tạo khóa và kiểm chứng giới hạn bản rõ ($190\text{ byte}$ với $2048\text{ bit}$, $318\text{ byte}$ với $3072\text{ bit}$).
2. **Bài 3.2 – RSA (Swing – `frm_RSA`):** Giao diện chọn kích thước khóa, tạo khóa, mã hóa/giải mã và quan sát tính ngẫu nhiên của đệm OAEP (cùng bản rõ cho ra các bản mã khác nhau ở mỗi lần mã hóa).
3. **Bài 3.3 – ECC / ECIES (`frm_ECC`):** Dùng đường cong `secp256r1` (P-256), sinh cặp khóa tạm (ephemeral), tính bí mật chung ECDH, dẫn xuất khóa qua HKDF-SHA256 và mã hóa bằng AES-256-GCM. Hỗ trợ `Save Keys` / `Load Keys` (tệp khóa thử nghiệm) và phát hiện giải mã sai khóa (`AEADBadTagException: Tag mismatch`).
4. **Bài 3.4 – Hệ mã lai RSA + AES-GCM (`frm_RSA_AES`):** Mã hóa văn bản dài trên $1.000$ ký tự bằng khóa phiên AES-256 ngẫu nhiên và dùng RSA-2048 bọc khóa AES. Tích hợp nút **Sửa 1 ký tự bản mã** để kiểm chứng khả năng phát hiện sai lệch toàn vẹn của AES-GCM.
5. **Bài 3.5 – Chữ ký số ECDSA (`frm_ECC_Signature`):** Ký thông điệp `THONG DIEP A` bằng khóa riêng và xác minh bằng khóa công khai (`VALID` màu xanh); phát hiện sửa thông điệp thành `THONG DIEP B` hoặc sai khóa công khai (`INVALID` màu đỏ).
6. **Tích hợp SQL Server (`frm_KetNoiSQL`, `frm_LichSu`):** Kết nối tới `LAB3_MaHoaKhoaCongKhai` bằng tài khoản đặc quyền tối thiểu `lab3_user`, tự động ghi nhận lịch sử thành công/thất bại vào bảng `dbo.History` và đối chiếu thống kê trên SSMS.

## 4. Hướng dẫn cài đặt và chạy chương trình

### Bước 1: Khởi tạo Cơ sở dữ liệu (SQL Server)
1. Mở **SQL Server Management Studio (SSMS)** và kết nối tới SQL Server (đã bật TCP/IP cổng `14333` và Mixed Mode).
2. Chạy lần lượt tệp `01_Tao_CSDL.sql` và `02_Tao_TaiKhoan_LAB.sql` để tạo cơ sở dữ liệu `LAB3_MaHoaKhoaCongKhai`, bảng `dbo.History` và tài khoản `lab3_user`.

### Bước 2: Chạy ứng dụng Java (NetBeans IDE)
1. Yêu cầu **JDK 17+** và **Apache NetBeans**.
2. Mở dự án `LAB3_MaHoaKhoaCongKhai` $\rightarrow$ chuột phải chọn **Clean and Build (`Shift + F11`)** để Maven tải driver `mssql-jdbc`.
3. **Chạy Bài 3.1 (Console):** Chuột phải `RSADemo.java` $\rightarrow$ **Run File (`Shift + F6`)**, nhập `2048` hoặc `3072` và bản rõ `HELLO RSA`.
4. **Chạy giao diện chính:** Nhấn **F6 (Run Project)** để mở cửa sổ `frm_Menu`.
5. Bấm **Kết nối SQL Server** $\rightarrow$ nhập thông tin (`127.0.0.1`, port `14333`, database `LAB3_MaHoaKhoaCongKhai`, user `lab3_user` và mật khẩu cục bộ) $\rightarrow$ bấm **Test Connection** trước khi chạy các bài **3.2 – 3.5** và **Xem lịch sử**.

### Bước 3: Kiểm tra thống kê trên SSMS
* Mở và chạy tệp `03_KiemTra_LichSu.sql` trong SSMS để xem 30 bản ghi mới nhất và bảng thống kê số lần thực hiện theo từng thuật toán và thao tác.

## 5. Lưu ý bảo mật & Video Demo (Lab 12)

* **Bảo mật:** Không ghi cứng mật khẩu SQL Server vào mã nguồn; mọi tệp khóa minh họa đều là khóa thử nghiệm và đã che phần khóa riêng (`PRIVATE=`) trong ảnh báo cáo.
* **Link YouTube:** [https://youtu.be/ngsLdSlkpJ0](https://youtu.be/ngsLdSlkpJ0)