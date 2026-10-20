# BÁO CÁO THỰC HÀNH LAB 7

- **Họ và tên:** Bùi Ngọc Huy
- **Mã số sinh viên:** 1150080137
- **Tên bài Lab:** Lab 7 KIỂM SOÁT NỘI DUNG WEB, CHỐNG MÃ ĐỘC VÀ QUẢN LÝ CHỨNG CHỈ TRÊN PFSENSE & WINDOWS SERVER
- **Link Video thực hành (YouTube):** (https://youtu.be/HAVFItebk34)

## 1. Nội dung đã thực hiện
- Triển khai môi trường IIS trên Windows Server (`C:\inetpub\wwwroot\files\`) kết hợp quản lý dịch vụ web (`net start W3SVC`) và xử lý xung đột bảo vệ thời gian thực của Windows Defender khi kiểm thử.
- Cấu hình Alias và Schedule trên pfSense: Thiết lập lịch trình giờ giấc (`LUNCH_WINDOW`, `TEST_NOW`) và các dải mạng, dịch vụ web mục tiêu để kiểm soát lưu lượng.
- Xây dựng hệ thống bộ lọc nội dung và kiểm soát truy cập trang web giáo dục/giải trí (`education.lab7.test`, `entertainment.lab7.test`) thông qua quy tắc tường lửa và proxy.
- Kiểm thử cơ chế phòng chống mã độc bằng tệp tin mẫu EICAR (`eicar.com`), xác thực phản ứng chặn tệp độc hại của hệ thống bảo mật và Windows Defender trên máy trạm.
- Quản lý nhật ký hệ thống (System Logs) trên pfSense (`Status > System Logs > Firewall`) để đối chiếu, theo dõi và ghi nhận các sự kiện chặn/cho phép kết nối.
- Quản lý và cấu hình chứng chỉ số: Tạo chứng chỉ CA nội bộ (`internal-ca` / `Lab7_CA`) trên pfSense, xuất file chứng chỉ và tiến hành import vào kho lưu trữ tin cậy (`Trusted Root Certification Authorities`) thông qua công cụ `certmgr.msc` trên Windows Server để thiết lập độ tin cậy HTTPS.

## 2. Kết quả thực hiện
- Hiểu rõ cơ chế kết hợp giữa Tường lửapfSense và dịch vụ Windows Server IIS trong việc kiểm soát truy cập, định tuyến và phân phối tài nguyên nội bộ.
- Nắm vững quy trình xử lý và nhận diện tệp tin mẫu mã độc (EICAR), minh chứng khả năng phản ứng của hệ thống bảo mật đối với các mối đe dọa trên mạng.
- Thành thạo việc cấu hình và quản lý chứng chỉ số SSL/TLS, giải quyết các lỗi cảnh báo chứng chỉ không tin cậy (*Untrusted Certificate*) bằng cách cấu hình và import CA gốc vào hệ thống Client.
- Khai thác hiệu quả hệ thống Nhật ký tường lửa (`Firewall Logs`) để kiểm tra, kiểm soát và xác thực trạng thái hoạt động của các luật bảo mật.

## 3. Các lưu ý
- Toàn bộ hình ảnh minh chứng thực tế (Hình 1 đến Hình 10) cùng các cấu hình chi tiết được trình bày đầy đủ trong file báo cáo Word đính kèm.
- Cần chú ý tạm tắt tính năng quét thời gian thực của Windows Defender khi thực hiện tạo tệp kiểm thử EICAR để tránh việc file bị xóa tự động trước khi ghi nhận kết quả.