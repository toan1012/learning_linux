# Lý thuyết CSDL

## Khái niệm cơ bản

Nhu cầu thực tế thực tế về tiếp nhân, xử lý, lưu trữ thông tin tạo ra yêu cầu phải trừu tượng hóa thông tin (information) thành dữ liệu (data) 
để phục vụ cho mục đích khai thác dữ liệu.

* Khi cần lưu trữ thông tin của hàng ngàn sinh viên : tên, tuổi, số điện thoại, môn học, lớp học, học lực,...-> cần có cơ sở dữ liệu.

* Kế toán muốn xuất báo cáo lương trong tháng, Giám đốc muốn lấy thông tin về 1 nhân viên,... tạo ra một nhu cầu phải có phần mềm ứng dụng 

* Yêu cầu truy xuất thông tin hiệu quả, chính xác, tạo CSDL -> phần mềm quản trị CSDL

Khái niệm CSDL: là một tập hợp dữ liệu biểu diễn một vài khía cạnh nào đó của đối tượng trong thế giới thức có liên hệ logic thống nhất
được thiết kế cho 1 mục đích nào đó.

Khái niệm hệ quản trị CSDL (Database Management System- DBMS) là hệ thống phần mềm cho phép: định nghĩa, tạo lập (bao gồm xác định kiểu, cấu trúc
ràng buộc dữ liệu, lưu trữ dữ liệu trên các thiết bị nhớ) và thao tác (truy xuất, cập nhật, kết xuât,..) các CSDL cho các ứng dụng khác nhau. 
Ví dụ: SQL server, MS Access, Oracle, FoxPro,...

Khái niệm Hệ cơ sở dữ liệu: là một hệ thống gồm 4 thành phần:
* Hệ quản trị CSDL
* Phần cứng
* CSDL và phần mềm ứng dụng.
* Những người sử dụng. 

Ví dụ: Hệ quản lý đào tạo, hệ quản lý nhân sự. Hình ảnh minh họa

![Imgur](https://i.imgur.com/dfsLvlT.png)

## DBMS

### Các tính năng của DBMS 

* Định nghĩa dữ liệu

* Quản lý lưu trữ

* Truy xuất dữ liệu một cách hiệu quả

* Biểu diễn các thao tác dữ liệu

* Xử lý câu hỏi

* Quản trị giao dịch

* Hỗ trợ ít nhất một mô hình dữ liệu

* Đảm bảo tính độc lập dữ liệu

* Hỗ trợ các ngôn ngữ cấp cao nhất định cho phép người sử dụng định nghĩa cấu trúc của dữ liệu, truy nhập và thao tác dữ liệu

* Điều khiển truy nhập

* Phục hồi dữ liệu

### Các ngôn ngữ sử dụng cho DBMS

* Ngôn ngữ định nghĩa dữ liệu (Data Definiton Language -DDL)

* Ngôn ngữ thao tác dữ liệu (DML)

* Ngôn ngữ điều khiển dữ liệu DCL) 

### Các mức trừu tượng dữ liệu trong DBMS

Mục đích chính của việc chia thàng 3 mức trừu tượng đó là để cách ly người dùng với cơ sở dữ liệu vật lý - mức mà người dùng thường không cần biết 
và cũng khó có thể nắm bắt được.

![Imgur](https://i.imgur.com/vAUIxmA.png)

Mức vật lý: Là mức thấp nhất, nó mô tả cách dữ liệu thực sự được lưu trữ trong database. Chi tiết cấu trúc dữ ở mức này rất phức tạp

Mức logic: Mô tả kiểu lưu trữ của data trong database (ví dụ theo cột, hàng)

Mức view: Mô tả tương tác của người dùng với database.

Ví dụ: Chúng ta nói đang lưu trữ dữ liệu của khách hàng trong bảng khách hàng. Ở mức vật lý những bản ghi được mô tả như những block theo từng byte, gigabyte, terabyte trong 
bộ nhớ. Những chi tiết này thường ẩn với ngay cả programmer. 

Ở tâng logic. những bản ghi được mô tả như những trường và thuộc tính cùng với kiểu dữ liệu, mối quan hệ giữa các bản ghi. Lập trình viên thường làm việc ở mức này. 

Ở mức view. người dùng tương tác với hệ thống qua giao diện đồ họa, không cần quan tâm tới cách lưu trữ và kiểu lưu trữ,..

### Kiến trúc của DBMS

Gồm 3 thành phần chính: 

![Imgur](https://i.imgur.com/1L56afV.png)

**Quản lý lưu trữ** 

![Imgur](https://i.imgur.com/GZzYW21.png)

Yêu cầu: Lưu trữ và truy xuất dữ liệu trên các thiết bị nhớ.

Thực hiện: Tổ chức tối ưu dữ liệu trên thiết bị nhớ, tương tác hiệu quả với bộ quản lý tệp

**Xử lý câu hỏi**

![Imgur](https://i.imgur.com/djXiDbp.png)

Yêu cầu: tìm kiến dữ liệu trả lời cho một yêu cầu truy vấn.

Thực hiện: biến đổi truy vấn ở mức cao thành các yêu cầu có thể hiểu được bởi hệ CSDL. Lựa chọn cách tốt nhất để trả lời truy vấn.

**Thực hiện giao dịch**

Yêu cầu: Định nghĩa giao dịch: một tập các thao tác được xử lý như một đơn vị không thể chia cắt, đảm bảo tính đúng đắn nhất quán của dữ liệu.

Thực hiện: Quản lý điều khiển tương tranh và phát hiện lỗi và phục hồi CSDL.

### Phân loại

Dựa theo mô hình dữ liệu chia ra các mô hình: mạng, phân cấp, quan hệ, hướng đối tượng,...

Theo số lượng người dùng: single, multiuser

Theo tính phân tán: Tập trung và phân tán...

Ta sẽ quan tâm tới tiêu chí phân tán mà xét 2 kiểu CSDL tập trung và phân tán. 

**Các hệ CSDL tập trung**

Hệ CSDL cá nhân: một người sử dụng đơn lẻ vừa thiết kế, tạo lập CSDL, cập nhật, bảo trì dữ liệu, lập và hiển thị báo cáo.
→ đảm nhiệm vai trò: người quản trị CSDL, người viết chương trình ứng dụng, end-user.

Hệ CSDL trung tâm: dữ liệu được lưu trữ trên một máy tính trung tâm.






