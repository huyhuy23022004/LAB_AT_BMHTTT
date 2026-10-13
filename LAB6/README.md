# BÁO CÁO THỰC HÀNH LAB 6

- **Họ và tên:** Bùi Ngọc Huy
- **Mã số sinh viên:** 1150080137
- **Tên bài Lab:** Lab 6 XÁC THỰC ACTIVE DIRECTORY VÀ CHÍNH SÁCH TRUY CẬP TRÊN PFSENSE
- **Link Video thực hành (YouTube):** Word

## 1. Nội dung đã thực hiện
- Khởi tạo kiến trúc mạng 4 Interface: Tận dụng và chuẩn hóa pfSense gồm 4 giao diện mạng độc lập: WAN (NAT/Internet), MGMT (192.168.56.254/24 - chuyên biệt cho máy thật quản trị WebGUI), LAN (10.6.0.1/24 - kết nối DC01, Client-A, Client-B) và DMZ (172.16.6.1/24 - kết nối máy chủ DMZ-WEB).
- Tích hợp Active Directory qua LDAP: Khởi tạo LDAP Authentication Server trên pfSense kết nối tới Domain Controller (10.6.0.10/24, domain `lab6.test`) qua cổng 389 với tài khoản Bind `svc_pfsense_ldap`; kiểm tra xác thực người dùng thành công cho hai user `sv_a` và `sv_b` tại mục Diagnostics > Authentication.
- Cấu hình Captive Portal xác thực người dùng Domain: Kích hoạt dịch vụ Captive Portal trên interface LAN sử dụng backend xác thực từ Active Directory (LAB6-AD); kiểm thử đăng nhập chặn/mở phiên truy cập thành công với tài khoản `sv_a` và `sv_b`.
- Xây dựng hệ thống Firewall Aliases & Schedule: Thiết lập các Alias quản trị tập trung bao gồm Host Alias cho máy trạm (CLIENT_A: 10.6.0.21, CLIENT_B: 10.6.0.22, nhóm POLICY_CLIENTS), Port Alias (WEB_PORTS: 80, 443) và các Host Alias đại diện cho các nhóm website mô phỏng trong DMZ (GENERAL_SITE: .10, JOB_SITE: .20, SOCIAL_SITE: .30, RESTRICTED_SITE: .40); cấu hình Schedule thời gian (LUNCH_WINDOW, WORK_HOURS, TEST_NOW).
- Kịch bản 1 (Chặn website tìm việc cho cá nhân): Cấu hình rule Block CLIENT_A truy cập JOB_SITE (TCP/80) kèm bật ghi log, đặt trên rule Pass GENERAL_SITE; kiểm thử từ Client-A truy cập thành công trang .10 và bị drop khi vào trang .20.
- Kịch bản 2 (Cấm nhóm website bị hạn chế cho toàn bộ người dùng): Thiết lập rule Block nhóm POLICY_CLIENTS truy cập RESTRICTED_SITE (TCP/80) có ghi log; kiểm thử chứng minh cả Client-A và Client-B đều bị chặn triệt để khi vào trang .40.
- Kịch bản 3 (Kiểm soát mạng xã hội theo khung giờ): Cấu hình cặp rule cho Client-B gồm Pass truy cập SOCIAL_SITE kèm schedule LUNCH_WINDOW (hoặc TEST_NOW) đặt phía trên rule Block; kiểm thử chứng minh Client-B chỉ vào được trang .30 khi trong khung giờ cho phép và bị chặn ngay khi ngoài giờ.
- Kịch bản 4 (Giới hạn dịch vụ Web trong giờ làm việc): Thiết lập rule Pass nhóm POLICY_CLIENTS đi cổng WEB_PORTS (80/443) gắn schedule WORK_HOURS, đặt trên rule Block All; kiểm chứng trong giờ hành chính chỉ duyệt được HTTP/HTTPS, các giao thức khác (Ping/ICMP) bị chặn.
- Kịch bản 5 (Cô lập vùng DMZ khỏi mạng nội bộ): Thiết lập rule Block DMZ net truy cập LAN net trên tab DMZ; kiểm thử xác nhận chiều từ LAN truy cập sang web DMZ thành công (nhờ cơ chế Stateful), nhưng chiều từ DMZ-WEB chủ động kết nối/ping sang DC01 trong mạng LAN bị ngăn chặn hoàn toàn.

## 2. Kết quả thực hiện
- Hiểu rõ ranh giới kỹ thuật giữa Authentication và Packet Filtering: pfSense base firewall lọc gói tin ở Lớp 3/Lớp 4 (dựa trên Source/Destination IP, Port, Protocol, Interface và Thời gian Schedule), không sử dụng trực tiếp LDAP username làm điều kiện lọc trong ruleset tường lửa; việc kiểm soát danh tính người dùng được ánh xạ kết hợp thông qua Captive Portal và IP tĩnh.
- Cơ chế quản trị tách biệt (Out-of-Band Management): Việc tách giao diện quản trị WebGUI sang subnet MGMT riêng biệt giúp quản trị viên duy trì quyền kiểm soát tường lửa an toàn, độc lập hoàn toàn với việc bật Captive Portal hay các bộ lọc chặn gắt gao trên mạng LAN của người dùng.
- Nguyên tắc First-match wins và cặp rule theo lịch trình: Khi một rule có gắn Schedule hết hiệu lực (Inactive), rule đó sẽ biến mất khỏi bảng xét duyệt; do đó bắt buộc phải có một rule Block rõ ràng (Explicit Deny) nằm ngay phía dưới để chặn lưu lượng ngoài giờ.
- Mô hình Stateful Inspection trong bảo mật DMZ: Tường lửa tự động cho phép gói tin phản hồi (Return traffic) của các phiên do LAN khởi tạo sang DMZ, trong khi rule Block trên tab DMZ ngăn chặn triệt để chiều tấn công ngược từ máy chủ công cộng bị xâm nhập vào mạng nội bộ.

## 3. Các lưu ý
- Toàn bộ chi tiết cấu hình các kịch bản 1 đến 5, câu trả lời phân tích chuyên sâu cho 12 câu hỏi lý thuyết và đầy đủ các hình ảnh minh chứng thực tế được trình bày chi tiết trong file báo cáo Word đính kèm.