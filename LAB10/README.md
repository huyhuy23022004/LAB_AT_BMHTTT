# BÁO CÁO THỰC HÀNH LAB 10

- **Họ và tên:** Bùi Ngọc Huy
- **Mã số sinh viên:** 1150080137
- **Tên bài Lab:** LAB 10 - MÃ HÓA ĐỐI XỨNG CỔ ĐIỂN VÀ ỨNG DỤNG
- **Link Video thực hành (YouTube):** https://youtu.be/mIOsUaR2POw

## 1. Nội dung đã thực hiện
- **Xây dựng lớp đọc/ghi tệp tiện ích `FileTextIO` và giao diện chuẩn:**
  - Phát triển lớp tiện ích `FileTextIO.java` hỗ trợ chọn tệp (`JFileChooser`), đọc và ghi tệp văn bản (`.txt`) tương tác trực tiếp với các component giao diện Swing (`JTextArea`).
- **Hiện thực 6 thuật toán mật mã đối xứng cổ điển:**
  - **Mật mã Caesar (`CaesarCipher.java`, `frm_Caesar.java`):** Mã hóa thay thế đơn bảng dịch chuyển ký tự theo khóa số nguyên $k \in \mathbb{Z}$. Hỗ trợ mã hóa, giải mã và mở/lưu tệp văn bản.
  - **Mật mã Vigenère (`VigenereCipher.java`, `frm_Vigenere.java`):** Mã hóa thay thế đa bảng sử dụng chuỗi khóa lặp để thực hiện phép cộng modulo 26 trên các chữ cái $A-Z$.
  - **Mật mã Rail Fence (`RailFenceCipher.java`, `frm_RailFence.java`):** Mã hóa hoán vị xáo trộn vị trí ký tự theo hình đường ziczac với số hàng $k$.
  - **Mật mã Playfair (`PlayfairCipher.java`, `frm_Playfair.java`):** Mã hóa thay thế theo cặp ký tự (digram) dựa trên ma trận $5 \times 5$ (gộp I/J), tự động chèn ký tự đệm `X` khi có cặp chữ trùng hoặc độ dài lẻ.
  - **Mật mã Transposition (`TranspositionCipher.java`, `frm_Transposition.java`):** Mã hóa hoán vị cột dựa trên mảng thứ tự thứ tự hoán vị (ví dụ: `2,3,1`).
  - **Mật mã Autokey (`AutokeyCipher.java`, `frm_Autokey.java`):** Nối tiếp bản rõ vào sau chuỗi khóa mồi ban đầu để tạo dòng khóa tự động có độ dài bằng bản rõ.
- **Tích hợp Cơ sở dữ liệu Microsoft SQL Server lưu trữ lịch sử:**
  - Thiết kế cơ sở dữ liệu `LAB1_MaHoaCoDien` và bảng `dbo.history` lưu vết lịch sử thao tác (`id`, `created_at`, `algorithm`, `operation`, `input_text`, `result_text`).
  - Đóng gói cấu hình kết nối an toàn qua `DbConfig.java` (kết nối SQL Server qua cổng TCP 14333, mật khẩu lưu trong bộ nhớ tạm runtime, không hardcode vào mã nguồn).
  - Viết lớp `HistoryDao.java` thực hiện chèn dữ liệu tự động và truy vấn hiển thị 50 thao tác mới nhất lên `JTable`.
  - Xây dựng giao diện cấu hình kết nối SQL Server (`frm_KetNoiSQL.java`) và giao diện xem lịch sử thao tác (`frm_LichSu.java`).
- **Phát triển ứng dụng tổng hợp (`CipherTongHop.java`, `frm_TongHop.java`):**
  - Tích hợp trọn vẹn 6 thuật toán cổ điển vào một màn hình duy nhất, cho phép chuyển đổi thuật toán qua `JComboBox`.
  - Tự động ép kiểu khóa phù hợp với từng thuật toán và tự động lưu vết lịch sử vào SQL Server mỗi khi thực hiện mã hóa/giải mã.
- **Triển khai mô phỏng phân tích và thám mã (`AttackDemos.java`, `frm_TanCong.java`):**
  - **Tấn công Vét cạn (Brute-Force Caesar):** Thử toàn bộ 26 khả năng dịch chuyển khóa $k$, áp dụng thuật toán chấm điểm tần suất tiếng Anh (`englishScore`) để tự động nhận diện và đánh dấu bản rõ có nghĩa.
  - **Phân tích tần suất chữ cái (Frequency Analysis):** Thống kê số lần và tỷ lệ phần trăm xuất hiện của các ký tự $A-Z$ trong bản mã để suy đoán quy luật thay thế.
  - **Tấn công biết trước bản rõ (Known Plaintext Attack):** Tải tái dựng chuỗi khóa Vigenère lặp lại dựa trên một cặp bản rõ và bản mã đã biết.
- **Xây dựng Giao diện Menu trung tâm (`frm_Menu.java`):**
  - Thiết kế bảng điều khiển tập trung hỗ trợ mở linh hoạt 9 cửa sổ chức năng độc lập (6 form thuật toán đơn, form tổng hợp, form thám mã, form kết nối CSDL).

## 2. Kết quả thực hiện
- **Nắm vững nguyên lý hoạt động của các họ mật mã cổ điển:**
  - Thấu hiểu cơ chế mã hóa thay thế đơn bảng (Caesar), thay thế đa bảng (Vigenère, Autokey), thay thế theo khối cặp ký tự (Playfair) và mã hóa hoán vị vị trí (Rail Fence, Transposition).
  - Phân biệt rõ sự khác nhau giữa mật mã biến đổi tần suất ký tự (mật mã thay thế) và mật mã giữ nguyên 100% tần suất ký tự (mật mã hoán vị).
- **Thành thạo kỹ năng lập trình Java Swing và kiến trúc phần mềm:**
  - Thiết kế giao diện chuẩn mực, tách biệt rõ ràng giữa các lớp xử lý thuật toán (Business Logic), lớp thao tác dữ liệu (DAO/Config) và các lớp giao diện (GUI).
  - Xử lý mượt mà việc đọc/ghi tệp văn bản UTF-8 và bắt lỗi ngoại lệ dữ liệu đầu vào.
- **Làm chủ kỹ thuật kết nối CSDL trong ứng dụng Java Desktop:**
  - Cấu hình thành công Microsoft SQL Server (TCP/IP Port 14333, Authentication Login), nạp driver JDBC `mssql-jdbc` và thao tác dữ liệu an toàn qua `PreparedStatement` / `ResultSet`.
  - Quản lý trạng thái kết nối CSDL linh hoạt (ứng dụng vẫn vận hành offline bình thường nếu không có SQL Server và tự động lưu lịch sử khi kết nối khả dụng).
- **Thấu hiểu bản chất an toàn thông tin & Kỹ thuật thám mã:**
  - Đánh giá được điểm yếu an ninh cốt lõi của mật mã cổ điển: không gian khóa quá nhỏ (Caesar), tính chu kỳ lặp khóa (Vigenère), bảo toàn tần suất ký tự (Rail Fence, Transposition).
  - Hiểu sâu các phương pháp thám mã qua vét cạn không gian khóa, phân tích tần suất n-gram và khai thác lỗ hổng từ bản rõ đã biết.

## 3. Các lưu ý
- Toàn bộ 16 ảnh chụp minh chứng thực tế cho từng giai đoạn (cấu hình SQL Server, 6 giao diện thuật toán đơn, form tổng hợp, form lịch sử, kết quả query SSMS và 3 thí nghiệm thám mã) được trình bày đầy đủ trong file báo cáo Word đính kèm.
- Khi triển khai dự án trên máy mới, cần đảm bảo dịch vụ SQL Server Browser đang bật, mở cổng TCP 14333 trong SQL Server Configuration Manager và add thư viện `mssql-jdbc` vào project NetBeans.
- Các công cụ và kỹ thuật thám mã được triển khai nghiêm ngặt phục vụ mục đích nghiên cứu học tập, giúp người học nhận thức rõ lý do các hệ thống mật mã cổ điển không còn đảm bảo độ an toàn trong môi trường thực tế ngày nay.