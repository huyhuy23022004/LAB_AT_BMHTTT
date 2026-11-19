# LAB 11 (LAB 2): MÃ HÓA ĐỐI XỨNG HIỆN ĐẠI

* **Họ và tên:** [Điền Họ và Tên của bạn]
* **MSSV:** [Điền MSSV của bạn]
* **Lớp:** [Điền Mã Lớp]
* **Môn học:** An toàn thông tin / Bảo mật thông tin
* **Bài thực hành:** **LAB 11** Mã hóa đối xứng hiện đại
* **Video Demo (Lab 11):** [https://youtu.be/H8Xr0Vtv-Cg](https://youtu.be/H8Xr0Vtv-Cg)

## 1. Mục tiêu bài thực hành (Lab 11)

* Cài đặt và kiểm thử các thuật toán mã hóa đối xứng hiện đại trên nền tảng **Java (Swing GUI - Maven)** và **Python (Tkinter)**.
* Hiểu và áp dụng các chế độ hoạt động của mã khối (Block Cipher Modes): **CBC**, **GCM** (có xác thực toàn vẹn), **CTR**, cùng mã dòng **RC4**.
* Thực hành lưu trữ mật khẩu an toàn bằng hàm băm dẫn xuất khóa **PBKDF2-HMAC-SHA256** kết hợp **Salt** ngẫu nhiên và lưu lịch sử thao tác vào **Microsoft SQL Server**.

## 2. Cấu trúc thư mục dự án

```text
LAB11_MSSV_HoTen/ (hoặc LAB2_MSSV_HoTen/)
├── LAB2_MaHoaHienDai/               # Dự án Java Maven (NetBeans IDE)
│   ├── pom.xml                      # Khai báo thư viện mssql-jdbc & Bouncy Castle
│   └── src/main/java/week_02/
│       ├── CipherUtil.java          # Tiện ích xử lý Base64, HEX, PBKDF2, Random
│       ├── FileTextIO.java          # Đọc / ghi tệp văn bản (.txt)
│       ├── DbConfig.java            # Quản lý cấu hình kết nối SQL Server
│       ├── UserDao.java             # Xử lý Register / Login (PBKDF2 + Salt)
│       ├── HistoryDao.java          # Ghi và truy vấn lịch sử mã hóa/giải mã
│       ├── DESCipher.java           # Thuật toán DES (CBC/PKCS5Padding)
│       ├── TripleDESCipher.java     # Thuật toán TripleDES (DESede/CBC)
│       ├── AESCipher.java           # Thuật toán AES-256-GCM
│       ├── RC4Cipher.java           # Thuật toán mã dòng RC4
│       ├── TwofishCipher.java       # Thuật toán Twofish (Bouncy Castle)
│       ├── AESCTRCipher.java        # Thuật toán AES-256-CTR
│       ├── frm_Menu.java/.form      # Giao diện Menu chính
│       ├── frm_KetNoiSQL.java/.form # Giao diện cấu hình kết nối SQL Server
│       ├── frm_LichSu.java/.form    # Giao diện xem lịch sử dbo.crypto_history
│       ├── frm_DES.java/.form       # Giao diện DES
│       ├── frm_TripleDES.java/.form # Giao diện TripleDES
│       ├── frm_AES.java/.form       # Giao diện AES-GCM + Register/Login
│       ├── frm_RC4.java/.form       # Giao diện RC4
│       ├── frm_Twofish.java/.form   # Giao diện Twofish
│       └── frm_AES_CTR.java/.form   # Giao diện AES-CTR
├── LAB2_Python/
│   └── aes_cbc_gui.py               # Bài nâng cao 02: AES-256-CBC (Python Tkinter)
├── 01_Tao_CSDL.sql                  # Script tạo CSDL LAB2_MaHoaHienDai & các bảng
├── lab2_user.sql                    # Script tạo tài khoản đăng nhập SQL lab2_user
├── screenshots/                     # Ảnh chụp màn hình minh chứng chạy thử
└── README.md
```

## 3. Các chức năng đã hoàn thành

1. **2.1 DES Cipher:** Mã hóa và giải mã với khóa 8 byte (`DES/CBC/PKCS5Padding`), IV ngẫu nhiên 8 byte, hỗ trợ lưu/mở tệp bản mã.
2. **2.2 TripleDES (3DES) Cipher:** Mã hóa và giải mã với khóa 24 byte (`DESede/CBC/PKCS5Padding`).
3. **2.3 AES-256-GCM & Xác thực người dùng SQL Server:**
   * Đăng ký (`Register`) và đăng nhập (`Login`) lưu `salt` (16 byte) và `password_hash` (PBKDF2-HMAC-SHA256, 120.000 vòng lặp) vào bảng `dbo.app_users`.
   * Cấp `Ticket` phiên làm việc sau khi đăng nhập thành công để sử dụng chức năng mã hóa/giải mã AES-GCM.
4. **2.4 RC4 Cipher:** Cài đặt thuật toán mã dòng RC4 (KSA + PRGA), xuất/nhập bản mã định dạng HEX.
5. **2.5 Twofish Cipher:** Sử dụng thư viện Bouncy Castle (`TwofishEngine` chế độ CBC), khóa dẫn xuất SHA-256, sinh IV ngẫu nhiên 16 byte (HEX) và lưu kèm IV vào tệp.
6. **2.6 Bài tập nâng cao:**
   * **Bài 01 (Java Swing):** `AES-256-CTR` với khóa 256-bit và Nonce 96-bit ngẫu nhiên.
   * **Bài 02 (Python Tkinter):** `AES-256-CBC` với đệm PKCS7, hỗ trợ chế độ tự kiểm thử `--self-test` và giao diện đồ họa.
7. **Quản trị CSDL & Nhật ký:** Kết nối động tới SQL Server và tự động ghi nhật ký thao tác vào bảng `dbo.crypto_history`.

## 4. Hướng dẫn cài đặt và chạy chương trình

### Bước 1: Khởi tạo Cơ sở dữ liệu (SQL Server)
1. Mở **SQL Server Management Studio (SSMS)** và kết nối tới SQL Server (cổng `14333`).
2. Chạy lần lượt tệp `01_Tao_CSDL.sql` và `lab2_user.sql` để tạo cơ sở dữ liệu `LAB2_MaHoaHienDai` cùng tài khoản `lab2_user`.

### Bước 2: Chạy ứng dụng Java (NetBeans IDE)
1. Yêu cầu **JDK 17+** và **Apache NetBeans**.
2. Mở dự án `LAB2_MaHoaHienDai` $\rightarrow$ chuột phải chọn **Clean and Build** để Maven tự tải các thư viện (`mssql-jdbc`, `bcprov-jdk18on`).
3. Nhấn **F6** (Run Project) để mở cửa sổ chính `frm_Menu`.
4. Bấm **Kết nối SQL Server** $\rightarrow$ nhập thông tin kết nối (`127.0.0.1`, port `14333`, database `LAB2_MaHoaHienDai`, user `lab2_user`) $\rightarrow$ bấm **Test Connection** trước khi sử dụng bài **2.3 AES + Login** và **Xem lịch sử**.

### Bước 3: Chạy ứng dụng Python (Bài nâng cao 02)
1. Mở Command Prompt tại thư mục `LAB2_Python`.
2. Cài đặt thư viện `cryptography`:
   ```bash
   py -m pip install cryptography
   ```
3. Chạy kiểm thử tự động:
   ```bash
   py aes_cbc_gui.py --self-test
   ```
4. Mở giao diện đồ họa AES-256-CBC:
   ```bash
   py aes_cbc_gui.py
   ```

## 5. Video Demo minh chứng (Lab 11)

* **Link YouTube:** [https://youtu.be/H8Xr0Vtv-Cg](https://youtu.be/H8Xr0Vtv-Cg)