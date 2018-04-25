# Zabbix 3.4

## Phần 1: Tổng quan

### 1. Zabbix là gì

Zabbix được tạo ra bởi Alecxei Vladishev và hiện nay đang được phát triển bởi Zabbix SIA

Zabbix là là giải pháp sát được phân phối dưới dạng thương mại mã nguồn mở

Zabbix là phần mềm giám giát số lượng lớn các thông số của network cũng như tình trạng và tính toàn vẹn của các server. 

Zabbix sử dụng thông báo linh hoạt cho phép người dùng cấu hình email để xuất cảnh báo. Nó cho phép phản ứng nhanh với những vấn đề của server.
Nó cũng xuất ra những báo cáo hữu ích, có tính trực quan từ những dữ liệu thu thập được. 

Các báo cáo và thống kê các thông số được quản lý dựa trên nền web, đảm bảo khả năng theo dõi và quản lý từ bất cứ đâu.

Zabbix không mất phú, được viết và phân phối dưới lincense GPL2 hoàn toàn. 


### 2. Đặc trưng 

Khả năng thu thập dữ liệu.

* Khả năng sẵn sàng và hiệu năng kiểm tra cao

* Hỗ trợ giao thức SNMP (cả trapping và polling). IPMI, JMX, VMWare moniroring.

* Thu thập dữ liệu mong muốn theo từng khoảng thời gian

* Được thực hiện ở server/proxy và agents.

Khả năng định nghĩa ngưỡng linh hoạt

* Đó là khả năng định nghĩa ngưỡng cho các thông số giám sát, cho các trigger được gọi. Các giá trị
này được lưu trong cơ sở dữ liệu phía backend.

Khả năng cấu hình cảnh báo ở mức cao

* Thông báo gửi đi có thể được thay đổi tùy theo những tình hướng không có theo lịch đặt sẵn, tùy
theo người nhận, hay kiểu truyền thông.

* Tự động thực hiện lệnh điều khiển

Khả năng đưa ra biểu đồ thời gian thực. 

* Các đối tượng được giám sát ngay lập tức được biểu thị trên đồ thị

Khả năng giám sát bằng web

Khả năng mở rộng tùy chọn khung nhìn.

* Nhờ vào khả năng tạo ra những đồ thị có thẻ tùy chỉnh chưa nhiều đối tượng trong một view

* Bản đồ network

Khả năng lưu lịch sử dữ liệu.

* Dữ liệu được lưu trong database

* Có thể cấu hình lịch sử 

Dễ cấu hình nhờ

* nhóm việc giám sát kiểm tra thành từng templates

* Các templates có khả năng kế thừa từ templates. 

Khả năng phát hiện đối tượng của mạng

* Tự động phát hiện các thiết bị mạng

* agent tự động đăng ký

* phát hiện các file hệ thống, network interface và SNMP OIDs

Khả năng mở rộng nhờ Zabbix API

* Zabbix API cung cấp một giao diện lập trình cho các developer hoặc cho các phần mềm bên thức
3 có thể tích hợp vào.

Khả năng cấp quyền hệ thống

* Có thể cấp quyền giới hạn view cho user

* Việc xác thực cho user rất an toàn.

Khả năng hỗ trợ đầy nhiều nền tảng, giúp dễ dàng trong việc mở rộng agent

* Zabbix agent có khả năng hỗ trợ nhiều nền tảng: IBM AIX, FressBSD, NetBSD, OpenBSD, HP-UX,
Mac OSX, Solaris: 9,10,11; Window tất cả các bản desktop và server từ version XP. 

4. Kiến trúc, thành phần











 
