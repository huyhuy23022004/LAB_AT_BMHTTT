# LAB 3: NHẬN DIỆN VÀ ỨNG PHÓ CÁC MỐI ĐE DỌA ĐẾN AN TOÀN THÔNG TIN
*(Identifying and Responding to Information Security Threats)*

---

## 1. THÔNG TIN CHUNG
* **Họ và tên:** Bùi Ngọc Huy
* **Mã số sinh viên (MSSV):** 1150080137
* **Lớp:** 11_CNPM2
* **Môn học:** An toàn và bảo mật hệ thống thông tin
* **Năm học:** 2026 - 2027
* **Repository GitHub:** `https://github.com/huyhuy23022004/LAB_AT_BMHTTT`
* **Video Demo thực hành:** Word

---

## 2. MÔI TRƯỜNG & CÔNG CỤ THỰC HÀNH
* **Nền tảng ảo hóa:** VMware Workstation Pro 26H1 (Chế độ mạng mặc định: `Host-only`, Snapshot: `LAB3_CLEAN_20260914`).
* **Hệ điều hành máy ảo (Target VM):** Windows 11 25H2 x64 (OS Build 26200.9445, KB5124008).
* **Endpoint Protection:** Microsoft Defender Antivirus (Real-time protection & Tamper Protection: **ON**).
* **Công cụ giám sát & phân tích Telemetry:**
  * **Windows Event Log / Auditpol:** Giám sát xác thực (Logon/Logoff audit).
  * **Microsoft Sysinternals Sysmon v15.22:** Giám sát tiến trình và Registry (Schema 4.90).
  * **Microsoft Sysinternals Autoruns v14.3:** Phát hiện các điểm cắm chốt tự khởi động (Persistence).
  * **Microsoft Sysinternals Process Explorer v17.14:** Định danh tiến trình và dịch vụ lắng nghe cổng.
  * **Wireshark 4.6.8 + Npcap:** Bắt và phân tích gói tin loopback (HTTP) và mạng ngoài (TLS/HTTPS).
  * **Python 3.14.7:** Triển khai HTTP Server cục bộ và chạy script kiểm thử tải.
* **Gói dữ liệu thực hành:** `LAB3_Threats_Assets.zip`  
  *(SHA-256: `96236f95ce59d0cc37f9b7ab8fbb04522f21870cd0b53aad6e52da5a23655439`)*.

---

## 3. NỘI DUNG VÀ KỊCH BẢN THỰC HIỆNHỆ THỐNG (7 TÌNH HUỐNG)

Áp dụng quy trình chuẩn an toàn thông tin: **Baseline $\rightarrow$ Observe $\rightarrow$ Detect $\rightarrow$ Contain $\rightarrow$ Recover $\rightarrow$ Verify**.

### Tình huống 0: Baseline hệ thống
* Ghi nhận trạng thái chuẩn của máy trạm trước khi thử nghiệm: OS build, cấu hình Defender, chính sách Firewall, cấu hình mạng và danh sách tiến trình đang chạy.

### Tình huống 1: Lập Risk Register & Phân loại 5 nguồn mối đe dọa
* Thiết lập bảng quản lý rủi ro theo chuỗi: **Asset $\rightarrow$ Vulnerability $\rightarrow$ Threat $\rightarrow$ Risk $\rightarrow$ Control**.
* Phân loại 5 tình huống thực tế vào đúng 5 nhóm nguồn đe dọa:
  1. *Hành động vô ý* (Nhân viên xóa nhầm cấu hình).
  2. *Hành động cố ý* (Cài mã độc gián điệp, đánh cắp dữ liệu).
  3. *Thảm họa tự nhiên* (Mất điện kéo dài gây hỏng dữ liệu).
  4. *Lỗi kỹ thuật* (Lỗi đĩa cứng, crash phần mềm).
  5. *Lỗi quản lý* (Không ban hành/không kiểm soát chính sách backup và vá lỗi).

### Tình huống 2: Mã độc (Malware) – Kiểm chứng phát hiện bằng tệp chuẩn EICAR
* Sử dụng chuỗi chuẩn EICAR an toàn để kiểm chứng khả năng phát hiện tức thời (Real-time Detection) của Microsoft Defender.
* Thu thập bằng chứng cảnh báo trong Windows Security Protection History và lệnh PowerShell `Get-MpThreatDetection`.

### Tình huống 3: Tấn công mật khẩu & Phân tích nguy cơ Keylogging
* Kích hoạt chính sách kiểm toán Logon (Auditpol).
* Tạo tài khoản `lab3user`, sinh vết đăng nhập thành công (**Event ID 4624/4648**) và các lần thử sai có kiểm soát (**Event ID 4625**).
* Thực hiện đổi mật khẩu (Credential Rotation) và chứng minh mật khẩu cũ bị vô hiệu hóa hoàn toàn.
* Phân tích tại sao mật khẩu phức tạp không chống lại được Keylogger và vai trò của MFA/Passkey/FIDO2.

### Tình huống 4: Backdoor – Phát hiện cơ chế Persistence và Listener trái phép
* Cài đặt Sysmon 15.22 với file cấu hình mẫu `sysmon-lab.xml`.
* Tạo artefact kiểm thử lành tính: Run Key (`LAB3_Run_Demo`) và Scheduled Task (`LAB3_Persistence_Demo`).
* Sử dụng **Autoruns** và **Sysmon (Event ID 1, 11, 12, 13)** để phát hiện cơ chế khởi động ẩn.
* Dựng HTTP listener nội bộ trên `127.0.0.1:8080`, ánh xạ cổng về PID bằng PowerShell và xác minh tiến trình bằng **Process Explorer 17.14**.

### Tình huống 5: Sniffing, MITM & Spoofing – Đối chiếu HTTP và HTTPS
* Dùng Wireshark bắt gói tin trên loopback adapter để thấy dữ liệu truyền văn bản rõ (Plaintext Request URI) của HTTP.
* So sánh với lưu lượng TLS (HTTPS qua cổng 443) tới `example.com` để làm rõ: Dữ liệu payload được mã hóa hoàn toàn, chỉ còn lộ Metadata (IP, Port, Packet Size, Handshake).
* Phân tích các biến thể MITM và giới hạn của mã hóa đường truyền.

### Tình huống 6: DoS, DDoS và Mail Bombing
* **DoS:** Chạy script Python tạo tải giới hạn (50 request, 5 worker) cục bộ trên `127.0.0.1:8080`.
* **DDoS:** Phân tích dataset offline (`ddos_sample.csv` thuộc dải RFC 5737 TEST-NET) để giải thích sự phân tán IP nguồn và lý do rule firewall tĩnh không hiệu quả.
* **Mail Bombing:** Phân tích tập log offline (`mailbomb_sample.csv`), xác định các đột biến về số lượng thư gửi từ cùng một nguồn (Volume/Sender Spikes).

### Tình huống 7: Kỹ nghệ xã hội (Social Engineering) & Phishing
* Phân tích tệp mẫu email offline (`phishing_email.txt`), bóc tách 5 chỉ dấu lừa đảo: Cảm giác khẩn cấp giả tạo, mạo danh hiển thị, sai lệch domain, lệch Reply-To và dẫn dụ nhập credential.
* Phân loại 6 ca thực tế từ `social_engineering_cases.csv` theo các hình thức: Phishing, Spear Phishing, Watering Hole, Pretexting, Baiting và Quid Pro Quo.

---

## 4. CLEANUP, PHỤC HỒI & KIỂM CHỨNG TOÀN VẸN
* **Cleanup:** Xóa toàn bộ Run key, Scheduled Task, dừng tiến trình HTTP server và xóa tài khoản thử nghiệm `lab3user`.
* **Verify:** Kiểm tra lại registry, task và port để bảo đảm hệ thống sạch sẽ; đối chiếu sai khác qua `autoruns_diff.txt`.
* **Toàn vẹn chứng cứ:** Tạo mã băm SHA-256 cho toàn bộ tệp trong thư mục `C:\LAB3\Evidence\` và xuất ra file `evidence_sha256.csv`.
* **Revert Snapshot:** Đưa máy ảo về snapshot an toàn `LAB3_CLEAN_20260914`.

---

## 5. KẾT QUẢ ĐẠT ĐƯỢC (PASS / FAIL)

| Tình huống | Trạng thái | Ghi chú minh chứng |
| :--- | :---: | :--- |
| **TH0: Baseline** | **PASS** | Đầy đủ file log baseline và ảnh `H3_Baseline_Defender_Firewall.png`. |
| **TH1: Risk Register** | **PASS** | Hoàn thành bảng 5 tài sản/rủi ro và phân loại 5 nguồn mối đe dọa. |
| **TH2: Malware (EICAR)** | **PASS** | Defender tự động chặn/cách ly; ảnh `H4_ProtectionHistory_EICAR.png`. |
| **TH3: Password & Keylogger** | **PASS** | Bắt đủ Event ID 4624/4625/4648; xác thực thành công rotation; ảnh `H5_Event4625.png`. |
| **TH4: Backdoor & Persistence** | **PASS** | Bắt được Sysmon Event 1, Autoruns phát hiện Run key; ảnh `H6`, `H7`, `H8`. |
| **TH5: Sniffing & MITM** | **PASS** | Thấy plaintext trên HTTP loopback và mã hóa trên TLS; ảnh `H9`, `H10`. |
| **TH6: DoS/DDoS/Mail Bomb** | **PASS** | Đo tải localhost an toàn; phân tích log offline DDoS và Mail Bomb; ảnh `H10`. |
| **TH7: Social Engineering** | **PASS** | Bóc tách 5 chỉ dấu email lừa đảo; phân loại 6 kịch bản Social Engineering. |
| **Cleanup & Hash Verify** | **PASS** | Hệ thống sạch hoàn toàn; toàn bộ evidence được băm SHA-256 vào `evidence_sha256.csv`. |

---

## 6. LỖI GẶP PHẢI VÀ CÁCH KHẮC PHỤC
1. **Lỗi chặn tệp EICAR tức thời:** Khi dùng `Set-Content` ghi file kiểm thử EICAR, Defender chặn ngay lập tức khiến PowerShell ném ngoại lệ. Khắc phục bằng cách dùng khối `try ... catch` để bắt lỗi đúng theo tài liệu hướng dẫn.
2. **Khởi chạy Tool Sysinternals:** Lần đầu chạy Sysmon/Autoruns/Process Explorer cần tham số `-accepteula` hoặc chạy bằng quyền Administrator để tránh bị treo giao diện do bảng thỏa thuận người dùng.
3. **Capture Loopback trên Windows:** Cần cài đặt thành phần **Npcap** đi kèm với Wireshark để máy trạm Windows có thể bắt được gói tin nội bộ qua giao diện `Adapter for loopback traffic capture` trên dải `127.0.0.1`.