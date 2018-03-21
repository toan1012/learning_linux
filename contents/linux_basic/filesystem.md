# Hệ thống file linux

Mỗi hệ thống tập tin nằm trên một phân vùng đĩa cứng. Phân vùng giúp tổ chức các nội 
dung của đĩa theo loại dữ liệu chứa Ví dụ, các chương
trình quan trọng được yêu cầu để chạy hệ thống thường được lưu trữ trên một phân vùng
riêng biệt so với tệp chứa các tệp do người dùng thông thường sở hữu. Ngoài ra, các 
tệp tạm được tạo ra và bỏ đi trong quá trình hoạt động của Linux thường nằm trên
một phân vùng riêng; theo cách này, sử dụng tất cả các không gian có sẵn trên một phân 
vùng cụ thể có thể không ảnh hưởng đến hoạt động bình thường của hệ thống.


Trước khi bạn có thể bắt đầu sử dụng một hệ thống tập tin, cần gắn nó vào cây hệ thống 
tập tin tại điểm mountpoint. Mountpoint là một thư mục (có thể rỗng), nơi hệ thống tập tin 
được gắn vào (mounted). Đôi khi bạn cần phải tạo thư mục nếu 
nó chưa tồn tại. Nếu bạn gắn một hệ thống tập tin vào một thư mục không rỗng, trước 
đây nội dung của thư mục đó được che phủ và không thể truy cập cho đến khi hệ thống tập 
tin được tháo gắn kết. Do đó điểm gắn kết thường là thư mục rỗng.



Lệnh mount được sử dụng để đính kèm một hệ thống tập tin nào đó vào cây thư mục hiện tại.
Đối số bao gồm node thiết bị và điểm kết nối.

`$ mount /dev/sda5 /mnt`

Để bỏ thiết bị đã được mount thì ta dùng lệnh 

`$ umount /mnt`

Để hiện thông tin hệ thống tệp tin đã mount ta dùng lệnh 

`df -Th`

## Thư mục home

Trong bất kỳ hệ thống UNIX, mỗi người dùng có thư mục chính của riêng mình, thường được 
đặt dưới /home. Thư mục gốc / trên các hệ thống Linux hiện đại không chỉ là thư mục
chính của người sử dụng gốc. 

## Các thư mục nhị phân

Thư mục /bin có chứa các chương trình thực thi, các lệnh cần thiết được sử dụng trong chế
độ người dùng đơn và các lệnh cần thiết được yêu cầu bởi tất cả người dùng hệ thống, chẳng
hạn như ps, ls, cp. Các lệnh không cần thiết cho hệ thống trong chế độ người dùng đơn được
đặt trong thư mục /usr/bin, trong khi thư mục  /sbin được sử dụng cho các lệnh thiết
yếu liên quan đến quản trị hệ thống, chẳng hạn như ifconfig và shutdown. Ngoài ra còn có 
thư mục /usr/sbin cho các chương trình quản trị hệ thống ít cần thiết. 


## Thư mục /dev

Thư mục /dev chứa các nút thiết bị, một loại tệp sử dụng phần lớn các thiết bị phần cứng và
phần mềm, ngoại trừ các thiết bị mạng. Thư mục này rỗng trên phân vùng đĩa khi nó không được mount
nhưng nó chứa các mục được tạo ra bởi hệ thống udev, tạo ra và quản lý các nút thiết bị trên 
Linux, tạo chúng tự động khi các thiết bị được tìm thấy. Thư mục /dev chứa các mục như:

/dev/sda1
/dev/lp1
/dev/dvd1


## Thư mục /var

Thư mục /var chứa các tệp dự kiến ​​sẽ thay đổi về kích thước và nội dung khi hệ thống đang
 chạy (var là viết tắt của biến) chẳng hạn như các mục trong các thư mục sau:

* Các tệp nhật ký hệ thống: /var/log
* Các thư viện: /var/lib
* Hàng đợi: /var/spool
* Tệp chứa file tạm: /var/tmp
* Thư mục chính của FTP: /var/ftp
* Thư mục Web server: /var/www

Thư mục /var được đặt trong phân vùng riêng của nó để sự gia tăng về kích thước và số lượng 
của các tập tin không ảnh hưởng đến hệ thống.

## Thư mục /etc

Thư mục /etc là nơi chứa tập tin cấu hình hệ thống. Nó không chứa các chương trình nhị phân, 
mặc dù có một số tập lệnh thực thi. Ví dụ, tập tin resolv.conf. Các tệp tin như passwd, shadow và nhóm 
để quản lý tài khoản người dùng được tìm thấy trong thư mục /etc. Các kịch bản cấp độ hệ thống
 được tìm thấy trong các thư mục con của /etc. Ví dụ, /etc/rc2.d có chứa liên kết đến các kịch
bản để vào và rời khỏi level2. Một số bản phân phối Linux mở rộng nội dung của /etc. 
Ví dụ, Red Hat thêm thư mục con /etc/sysconfig chứa nhiều tệp cấu hình hơn.

## Thư mục /boot

Thư mục /boot chứa những tập tin cần thiết để khởi động hệ thống. Đối với kernel linux
được cài đặt trên hệ thống có bốn tệp:

* vmlinuz là hạt nhân Linux đã được nén, bắt buộc để khởi động.
* initramfs là ram được khởi tạo ban đầu, được yêu cầu để khởi động.
* config là file cấu hình kernel, chỉ được sử dụng để gỡ lỗi.
* System.map chứa bảng ký hiệu hạt nhân, chỉ được sử dụng để gỡ lỗi.
Mỗi tập tin này có một phiên bản kernel được nối vào tên của tập tin. Ví dụ vmlinuz-3.5.0-13-generic
ứng với kernel linux 3.5.0.13

## Thư mục /lib

Thư viện /lib chứa các thư viện (mã chung được chia sẻ bởi các ứng dụng và cần thiết cho ứng dụng 
để chạy) cho các chương trình thiết yếu trong thư mục /bin và /sbin. Hầu hết trong số này là những 
gì được gọi là thư viện được nạp động (còn được gọi là thư viện chia sẻ hoặc Shared Objects (SO)). 
Trên một số distro Linux, có thư mục /lib64: chứa các thư viện 64-bit, trong khi /lib 
chứa các phiên bản 32-bit. Các mô-đun hạt nhân (mã hạt nhân, thường là trình điều khiển thiết bị, có 
thể nạp và giải phóng mà không cần khởi động lại hệ thống) được đặt trong /lib/modules /.

## Các thư mục khác

|    Thư mục   |                                                                    Sử dụng                                                                   |
|:------------:|:--------------------------------------------------------------------------------------------------------------------------------------------:|
|     /opt     | Chứa  gói cài đặt phần mềm ứng dụng tùy chon                                                                                                 |
|     /sys     | Virtual pseudo-filesystem đưa ra thông tin về hệ thống và phần cứng. Có thể được sử dụng để thay đổi tham số hệ thống và cho mục đích gỡ lỗi |
|     /tmp     | Chứa các file tạm                                                                                                                            |
|     /usr     | Chứa nhiều ứng dụng đa người dùng, công cụ và dữ liệu                                                                                        |
| /usr/include | Tiêu đề file dùng để biên dịch ứng dụng                                                                                                      |
|   /usr/lib   | Các thư viện cho  binary programs                                                                                                            |
|   /usr/lib   | /usr/lib6464bit Libraries cho các chương trình nhị phân programs,                                                                            |
| /usr/src     | Source code cho Linux kernel                                                                                                                 |
| usr/local    | Dữ liệu và chương trình chạy trên máy local.                                                                                                 |
	
