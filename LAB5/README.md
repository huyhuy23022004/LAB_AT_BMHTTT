# BÁO CÁO THỰC HÀNH LAB 5

- **Họ và tên:** Bùi Ngọc Huy
- **Mã số sinh viên:** 1150080137
- **Tên bài Lab:** Lab 5 THIẾT LẬP MÔ HÌNH TƯỜNG LỬA PFSENSE
- **Link Video thực hành (YouTube):** 

## 1. Nội dung đã thực hiện
- Khởi tạo máy ảo & gán card mạng: Tạo máy ảo pfSense CE 2.7.2 (FreeBSD 64-bit) với 3 card mạng độc lập gồm WAN (Bridged), LAN 10.0.0.0/8 (Host-only) và DMZ 172.16.0.0/16 (Internal Network `dmz-net`).
- Cấu hình IP qua Console: Thiết lập thủ công IP giao diện LAN 10.0.0.1/8 trên màn hình Console pfSense, vô hiệu hóa DHCP Server để quản lý IP tĩnh.
- Triển khai Domain Controller: Cấu hình Windows Server với IP tĩnh 10.0.0.2/8, Gateway 10.0.0.1, cài đặt vai trò Active Directory Domain Services (Domain: `vietnam.local`) và thiết lập DNS Forwarder upstream ra 8.8.8.8.
- Quản trị qua WebGUI: Định cấu hình card mạng quản trị Host-only của máy thật (10.0.0.100/8), truy cập giao diện quản trị WebGUI pfSense, hoàn tất Setup Wizard và kích hoạt giao diện DMZ (172.16.0.1/16).
- Cấu hình Outbound NAT & Chuẩn hóa ruleset: Chuyển đổi Outbound NAT sang chế độ Hybrid (tự động ánh xạ cho cả LAN và DMZ); vô hiệu hóa các rule `Default allow` mặc định, giữ `Anti-Lockout Rule` và tạo rule nền tảng do quản trị viên kiểm soát; thực thi Reset States khi thay đổi luật.
- Tình huống 1 (Chặn ICMP, mở Web/DNS): Thiết lập thứ tự rule ưu tiên First-match wins (Block ICMP ở trên, Pass DNS cổng 53 và Pass HTTP/HTTPS cổng 80/443 ở dưới); kiểm thử bằng ping thất bại trong khi Resolve-DnsName và curl web thành công.
- Tình huống 2 (Cấp quyền truy cập Internet theo Host): Khởi tạo host kiểm thử LAN-Test (Ubuntu 10.0.0.3/8); áp dụng rule chỉ Pass IP 10.0.0.2 (DC) và Block toàn bộ dải LAN net còn lại; đo kiểm kết quả cách ly thành công giữa hai máy.
- Tình huống 3 (Cô lập DMZ khỏi LAN): Xây dựng máy chủ DMZ-Web (172.16.0.2/16); xác lập baseline thông mạng ban đầu, sau đó cấu hình rule Block DMZ net truy cập LAN net đặt trên rule Pass Any; chứng minh DMZ vẫn ra được Internet nhưng không thể truy cập tài sản nội bộ LAN.
- Tình huống 4 (Port Forwarding WAN → DMZ): Cài đặt dịch vụ IIS trên DMZ-Web; tắt chặn dải IP private trên WAN pfSense; thiết lập NAT Port Forward từ cổng WAN:8080 trỏ về DMZ-Web:80 và kiểm thử truy cập trang web IIS thành công từ máy thật ngoài WAN.
- Tình huống 5 (Giám sát Firewall Log): Kích hoạt tính năng ghi log trên rule Block; phát sinh lưu lượng vi phạm và tra cứu bản ghi chi tiết tại System Logs / Firewall để xác định gói tin bị drop.

## 2. Kết quả thực hiện
- pfSense áp dụng cơ chế đánh giá luật từ trên xuống dưới theo nguyên tắc First-match wins; thứ tự sắp xếp của rule (đặc biệt là vị trí giữa Block và Pass) mang tính chất quyết định toàn bộ hiệu lực của chính sách an ninh.
- Là một Stateful Firewall, pfSense duy trì bảng trạng thái phiên (State Table); khi sửa đổi chính sách tường lửa, thao tác Reset States là bắt buộc để hủy các phiên đã thiết lập trước đó, tránh việc đánh giá sai lệch kết quả kiểm thử.
- Vùng mạng DMZ giúp thiết lập vành đai bảo vệ cách ly máy chủ dịch vụ công cộng; trong trường hợp DMZ-Web bị xâm nhập, kẻ tấn công hoàn toàn bị chặn đứng, không thể trực tiếp quét hay tấn công leo thang sang Domain Controller trong mạng LAN.
- Phân biệt rõ vai trò kỹ thuật: NAT đảm nhận việc chuyển đổi/ánh xạ địa chỉ gói tin (Private - Public), trong khi Firewall Rules là bộ lọc chính sách an ninh quyết định việc cho phép hay ngăn chặn luồng dữ liệu đi qua.
- Thao tác loại bỏ các rule mặc định mở rộng (`Default allow`), tắt quản trị từ xa không an toàn, áp dụng nguyên tắc đặc quyền tối thiểu (Least Privilege) và bật tính năng lọc mạng riêng/bogon trên WAN là những biện pháp hardening cốt lõi cho hệ thống tường lửa.

## 3. Các lưu ý
- Toàn bộ chi tiết phân tích 6 câu hỏi chuyên sâu, các bảng thông số cấu hình và toàn bộ 19 hình ảnh minh chứng thao tác/kết quả đo kiểm được trình bày đầy đủ trong file báo cáo Word đính kèm trong thư mục này.