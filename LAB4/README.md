# BÁO CÁO THỰC HÀNH LAB 4

- **Họ và tên:** Bùi Ngọc HUY
- **Mã số sinh viên:** 1150080137
- **Tên bài Lab:** Lab 4 KHẢO SÁT VÀ ĐÁNH GIÁ BỀ MẶT MẠNG BẰNG NMAP
- **Link Video thực hành (YouTube):** [https://youtu.be/pNAV3V0m01c]

## 1. Nội dung đã thực hiện
- Thiết lập môi trường: Cấu hình đồng bộ card mạng ảo giữa máy quét Kali Linux (192.168.59.129) và máy mục tiêu Windows Server (192.168.59.128); kiểm tra thông mạng hai chiều bằng ping.
- Host Discovery: Sử dụng kỹ thuật dò tìm host (`-sn`) để lập danh sách kiểm kê các thiết bị và địa chỉ MAC/NIC đang hoạt động trong toàn bộ dải mạng /24.
- Khảo sát cổng TCP: Thực thi và so sánh cơ chế bắt tay của TCP Connect scan (`-sT`), SYN scan nửa mở (`-sS`); phân tích trạng thái cổng open/closed/filtered.
- Thăm dò chính sách lọc Firewall: Thực hiện các kỹ thuật quét cờ bất thường (FIN scan `-sF`, Xmas scan `-sX`, NULL scan `-sN`) và ACK scan (`-sA`) để phân biệt trạng thái filtered và unfiltered.
- Quét cổng UDP: Thực hiện quét có kiểm soát nhóm 20 cổng UDP phổ biến nhất (`-sU --top-ports 20`), phân tích nguyên nhân phản hồi chậm và trạng thái open|filtered.
- Nhận diện dịch vụ và hệ điều hành: Sử dụng `-sV` phân tích banner để xác định tên/phiên bản dịch vụ, `-O` nhận diện dấu vân tay hệ điều hành (OS fingerprinting) và quét nâng cao tổng hợp (`-A`).
- Kiểm tra an toàn bằng NSE Script: Thu thập thông tin định danh máy chủ (`smb-os-discovery`) và rà soát dấu hiệu lỗ hổng nghiêm trọng EternalBlue (`smb-vuln-ms17-010`) trên cổng SMB 445.
- Xuất hồ sơ bằng chứng: Lưu trữ kết quả quét ra các định dạng văn bản (`-oN`), XML (`-oX`), Grepable (`-oG`) và sử dụng `xsltproc` để xuất báo cáo trực quan dạng HTML.
- Đánh giá trước và sau khi Hardening: Đo kiểm và so sánh sự dịch chuyển trạng thái cổng trước/sau khi tắt dịch vụ thừa và kích hoạt tường lửa phòng thủ.

## 2. Kết quả thực hiện
- Kỹ thuật quét SYN (`-sS`) tối ưu về tốc độ, ít để lại dấu vết ứng dụng hơn TCP Connect (`-sT`) do không hoàn tất bắt tay 3 bước, tuy nhiên yêu cầu quyền quản trị (root/sudo) để thao tác gói tin thô (raw packet).
- ACK scan (`-sA`) không dùng để tìm cổng mở mà là công cụ hiệu quả để lập bản đồ tường lửa, xác định cổng nào cho phép gói tin đi qua (unfiltered) hoặc bị chặn drop (filtered).
- Các kỹ thuật FIN/Xmas/NULL scan bộc lộ hạn chế trên Windows Server do TCP/IP stack của hệ điều hành Windows luôn gửi RST cho mọi cổng (không tuân thủ hoàn toàn RFC 793).
- Việc nhận diện phiên bản dịch vụ (`-sV`) đóng vai trò then chốt trong quản lý lỗ hổng bảo mật; các mã CVE nguy hiểm luôn gắn với phiên bản phần mềm cụ thể chứ không thể chỉ dựa vào số hiệu cổng mặc định.
- Kỹ thuật phòng thủ hiệu quả nhất là giảm thiểu bề mặt tấn công (tắt cổng/dịch vụ thừa, giới hạn IP nguồn trên Firewall và cập nhật bản vá thường xuyên) thay vì chỉ trông cậy vào việc che giấu thông tin.

## 3. Các lưu ý
- Toàn bộ chi tiết phân tích 10 câu hỏi lý thuyết và đầy đủ 8 hình ảnh minh chứng bắt buộc nằm trong file báo cáo Word đính kèm trong thư mục này.
