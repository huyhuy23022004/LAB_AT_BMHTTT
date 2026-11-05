# BÁO CÁO THỰC HÀNH LAB 9

- **Họ và tên:** Bùi Ngọc Huy
- **Mã số sinh viên:** 1150080137
- **Tên bài Lab:** LAB 9 - REMOTE DNS CACHE POISONING TRONG MÔI TRƯỜNG pfSense CÔ LẬP
- **Link Video thực hành (YouTube):** https://youtu.be/3RJu1d0c_rs

## 1. Nội dung đã thực hiện
- **Thiết lập phân vùng mạng và Tường lửa pfSense:**
  - Kế thừa hạ tầng mạng từ Lab trước, thêm card mạng cho pfSense để tạo phân vùng cô lập `ATTACKNET` (`10.10.0.1/24`).
  - Chuyển máy tấn công Kali Linux sang subnet mới (`10.10.0.200/24`), đồng thời thiết lập chế độ `Promiscuous Mode = Deny` trên vSwitch để triệt tiêu hoàn toàn khả năng nghe lén gói tin cục bộ.
  - Cấu hình ma trận luật tường lửa (Firewall Ruleset) chặt chẽ trên pfSense: chỉ mở các luồng dịch vụ tối thiểu cần thiết cho bài lab (DNS query, HTTP, race relay) và chặn toàn bộ lưu lượng không liên quan hay ra Internet.
- **Khảo sát và nghiệm thu Baseline:**
  - Thiết lập dịch vụ phân giải tên miền **BIND9** trên `DNS-SERVER` (`10.9.0.10`) với forward zone `example.lab10.test` trỏ sang máy chủ ủy quyền `AUTH-DNS` (`172.16.6.20`).
  - Khởi chạy script dịch vụ `auth_dns_relay_lab10.py` trên `AUTH-DNS` phục vụ phân giải chính thống và mở cổng race harness UDP 5300.
  - Kiểm chứng máy trạm `VICTIM` (`10.9.0.100`) phân giải chính xác tên miền `www.example.lab10.test` trả về địa chỉ IP trang web hợp lệ `172.16.6.10`.
- **Chứng minh tính chất "Remote" (Chống Sniffing):**
  - Chạy `tcpdump -ni any udp port 53` trên Kali Linux trong khi máy VICTIM liên tục thực hiện truy vấn DNS sang Resolver.
  - Xác nhận kết quả bắt gói trên Kali đạt `0 packets captured`, chứng minh kiến trúc định tuyến của pfSense đã cô lập hoàn toàn lưu lượng giữa `DNS-SERVER` và `AUTH-DNS`, buộc kẻ tấn công phải thực hiện tấn công mù từ xa.
- **Triển khai dịch vụ mô phỏng có chủ đích (Attack Phase):**
  - Tạm dừng dịch vụ BIND9 trên `DNS-SERVER` và chạy resolver mô phỏng `vuln_resolver_lab10.py` nhằm tái hiện điểm yếu cố định cổng nguồn UDP `33333` và cho phép truy vấn từ subnet của Attacker.
  - Trên máy Kali Linux, khởi tạo các dịch vụ giả mạo gồm máy chủ tên miền độc hại `evil_dns_lab10.py` (cổng UDP 53) và máy chủ web giả mạo `http.server 80` chứa giao diện `fake_index.html`.
- **Thực thi tấn công Remote Cache Poisoning (Kaminsky Simulation):**
  - Kích hoạt script tấn công `kaminsky_remote_lab10.py` trên Kali Linux: liên tục gửi truy vấn với các tiền tố tên miền con ngẫu nhiên nhằm vượt qua cơ chế lưu đệm (cache effect/TTL) của Resolver.
  - Tận dụng race relay trên `AUTH-DNS` (cổng 5300) để mô phỏng không gian đoán Transaction ID 16-bit ($2^{16} = 65,536$ giá trị), gửi gói tin phản hồi giả mạo về cổng nguồn `33333` của Resolver.
  - Gói phản hồi độc hại chèn bản ghi ủy quyền giả mạo `NS ns.evil.example.lab10.test` kèm bản ghi Glue A trỏ về IP của Kali (`10.10.0.200`).
- **Xác thực kết quả đầu độc và Bắt gói tin minh chứng:**
  - Ghi nhận thành công các dòng log `[PENDING]` và `[HIT]` trên màn hình `AUTH-DNS`, cùng log `[CACHE] delegation poisoned` trên `DNS-SERVER`.
  - Trên máy VICTIM, thực hiện xóa cache và kiểm chứng kết quả truy vấn `www.example.lab10.test`: hệ thống bị chuyển hướng thành công sang IP của Attacker (`10.10.0.200`), lệnh `curl` tải về thành công giao diện trang web mô phỏng giả mạo.
  - Sử dụng `tcpdump` trên `DNS-SERVER` bắt trọn vẹn luồng gói tin truy vấn từ cổng cố định `10.9.0.10:33333` và gói phản hồi giả mạo gửi ngược về từ `AUTH-DNS`.
- **Khôi phục hệ thống và Áp dụng giải pháp Hardening:**
  - Tắt resolver mô phỏng, kích hoạt lại dịch vụ BIND 9.18 tiêu chuẩn và xóa sạch bộ nhớ đệm qua lệnh `rndc flush`.
  - Thu hẹp chính sách truy vấn đệ quy (`allow-recursion`), loại bỏ hoàn toàn quyền truy vấn của phân vùng không tin cậy `ATTACKNET`.
  - Bắt gói tin kiểm chứng cơ chế **Source-Port Randomization (SPR)**: xác nhận các truy vấn đệ quy của BIND9 sử dụng cổng nguồn UDP biến thiên ngẫu nhiên liên tục thay vì cổng tĩnh `33333`.

## 2. Kết quả thực hiện
- **Nắm vững bản chất tấn công Remote DNS Cache Poisoning:** Hiểu rõ sự khác biệt giữa tấn công Local DNS Spoofing (nghe lén trực tiếp TXID) và tấn công Remote (phải đoán "mù" TXID và cổng nguồn UDP từ xa qua mạng định tuyến).
- **Thấu hiểu cơ chế tấn công Kaminsky:** Nắm rõ kỹ thuật sử dụng chuỗi tên miền con ngẫu nhiên (random non-existent subdomains) để buộc Resolver luôn phải truy vấn upstream, vượt qua rào cản TTL của cache; hiểu rõ vai trò của bản ghi ủy quyền `NS` kết hợp bản ghi `Glue A` trong Authority/Additional Section để chuyển hướng toàn bộ phân vùng (zone delegation).
- **Đánh giá không gian xác suất toán học:**
  - Khi cổng nguồn cố định, kẻ tấn công chỉ cần vét cạn không gian 16-bit TXID ($2^{16} = 65,536$ giá trị).
  - Khi có cơ chế ngẫu nhiên hóa cổng nguồn (Source-Port Randomization theo RFC 5452), không gian tìm kiếm mở rộng lên mức $2^{16} \times 2^{16} \approx 4.29 \times 10^9$ khả năng, triệt tiêu khả năng brute-force trong thời gian thực.
- **Hiểu sâu các nguyên tắc phòng thủ và Hardening DNS:**
  - Cơ chế kiểm tra thẩm quyền phạm vi (Bailiwick checking) ngăn chặn việc chèn bản ghi ngoài vùng.
  - Phân đoạn mạng (Network Segmentation) và quy tắc đặc quyền tối thiểu của tường lửa pfSense.
  - Kiểm soát truy vấn đệ quy (Recursion ACL) ngăn chặn Open Resolver.
  - Vai trò của chữ ký số mật mã học trong DNSSEC (Domain Name System Security Extensions) bảo vệ toàn vẹn dữ liệu DNS ngay cả khi TXID và cổng nguồn bị đoán trúng.

## 3. Các lưu ý
- Toàn bộ 9 đầu mục hình ảnh minh chứng thực tế cho từng giai đoạn, bảng so sánh đối chiếu kỹ thuật (Baseline / Attack / Restore) và lời giải chi tiết cho 12 câu hỏi phân tích lý thuyết được trình bày đầy đủ trong file báo cáo Word đính kèm.
- Bài thực hành tuân thủ nghiêm ngặt phạm vi an toàn đạo đức mạng: chỉ thao tác trên các dải mạng ảo cô lập (`10.9.0.0/24`, `10.10.0.0/24`, `172.16.6.0/24`) và sử dụng tên miền thử nghiệm `.test` (RFC 2606), tuyệt đối không áp dụng trên môi trường mạng thật hay Internet.
- Toàn bộ các rule kiểm thử đặc thù của bài lab (như rule cho phép UDP 5300 của race harness) được tắt/xóa bỏ sau khi hoàn thành để đảm bảo trạng thái an toàn của hệ thống pfSense.