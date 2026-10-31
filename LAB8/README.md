# BÁO CÁO THỰC HÀNH LAB 8

- **Họ và tên:** Bùi Ngọc Huy
- **Mã số sinh viên:** 1150080137
- **Tên bài Lab:** Lab 8 LOCAL DNS ATTACK - DNS SPOOFING VÀ CACHE POISONING TRONG MẠNG ẢO CÔ LẬP
- **Link Video thực hành (YouTube):** https://youtu.be/5C9a8Md1sCQ (máy lag nên quay được khúc đầu khúc sau bị drop mất)

## 1. Nội dung đã thực hiện
- Xây dựng mạng ảo cô lập DNS-LAB (`10.9.0.0/24`) phía sau tường lửa pfSense bằng cách thêm Adapter 5, gán Interface `DNSLAB` (`10.9.0.1/24`) và thiết lập các rule tường lửa cô lập tuyệt đối với các phân vùng mạng khác.
- Cấu hình DNS-SERVER (Ubuntu Server 24.04) chạy dịch vụ **BIND9** làm Authoritative cho zone `lab9.test` và Recursive/Caching Resolver cho toàn bộ dải mạng lab, đồng thời trỏ forward zone external tới mô phỏng `AUTH-DNS` (`172.16.6.20`).
- Cấu hình máy trạm VICTIM (`10.9.0.100`) sử dụng duy nhất DNS-SERVER nội bộ để thực hiện phân giải tên miền.
- Cấu hình ATTACKER (Kali Linux tại `10.9.0.200`) chạy dịch vụ web giả lập (`fake_index.html`) và chuẩn bị công cụ Scapy/Wireshark với chế độ `Promiscuous Mode = Allow VMs`.
- Thực hiện **Task 1 (Hosts File Redirection):** Thay đổi tệp cục bộ `/etc/hosts` trên Victim để chuyển hướng trực tiếp tên miền `www.lab9.test` về IP của Attacker nhằm kiểm chứng tính ưu tiên của phân giải tĩnh so với hệ thống DNS.
- Thực hiện **Task 2 (Direct DNS Spoofing):** Quan sát truy vấn từ Victim ra DNS-SERVER trên Kali, sử dụng script python kết hợp Scapy để gửi phản hồi DNS giả mạo trực tiếp chứa Transaction ID và UDP port trùng khớp, lừa Victim nhận địa chỉ IP của Attacker.
- Thực hiện **Task 3 (DNS Cache Poisoning):** Tấn công đầu độc bộ nhớ đệm của máy chủ phân giải trung tâm BIND9 bằng cách giả mạo phản hồi trả về từ Authoritative DNS trước phản hồi hợp lệ, xác thực bản ghi độc hại được lưu trong cache qua lệnh `rndc dumpdb -cache`.

## 2. Kết quả thực hiện
- Hiểu rõ bản chất hoạt động của phân giải tên miền phân cấp, cơ chế tương tác giữa client, recursive resolver và authoritative name server.
- Nắm vững nguyên lý cốt lõi của các lỗ hổng tấn công DNS kinh điển (Direct Spoofing và Cache Poisoning) dựa trên sự trùng khớp của Transaction ID và cổng nguồn UDP.
- Thành thạo việc phân tích gói tin DNS bằng Wireshark/tcpdump, nhận diện sự khác biệt giữa phản hồi hợp lệ và phản hồi giả mạo trong "cuộc đua phản hồi" (*race condition*).
- Nắm bắt và áp dụng hiệu quả các biện pháp phòng thủ chuyên sâu (*hardening*): kiểm soát lưu lượng DNS egress, phân đoạn mạng, cấu hình tường lửa pfSense và sử dụng các cơ chế bảo vệ như DNSSEC hoặc giới hạn cache.

## 3. Các lưu ý
- Toàn bộ hình ảnh minh chứng thực tế cho từng task cùng câu trả lời phân tích chi tiết cho các câu hỏi lý thuyết được trình bày đầy đủ trong file báo cáo Word đính kèm.
- Cần tuân thủ nghiêm ngặt phạm vi an toàn, chỉ thực hiện script và tấn công bên trong mạng ảo cô lập `DNS-LAB`, tuyệt đối không chạy trên mạng thật hay Wi-Fi trường.