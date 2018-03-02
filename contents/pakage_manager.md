
# Hệ thống quản lý gói

Các phần lõi của bản phân phối Linux và hầu hết các phần mềm bổ sung được cài đặt thông qua Hệ thống quản lý Gói. 
 Các package có thể phụ thuộc lẫn nhau. Có hai họ quản lý package là: quản lý dựa theo dpkg và rpm. Đây là những trình quản lý
ở cấp thấp. Hai hệ thống này không tương thích với nhau, nghĩa là không dùng lệnh dpkg để cài gói rpm.

| **Công cụ cấp cao** | **Công cụ cấp thấp** | **Họ**      |
|-----------------|------------------|---------|
| apt-get         | dpkg             | Debian  |
| zypper          | rpm              | SUSE    |
| yum             | rpm              | Red Hat |



 Ở trên ta thấy có 2 nhóm: một là công cụ cấp thấp (như dpkg hoặc rpm) chỉ chú ý đến các chi tiết của việc 
giải nén các gói riêng lẻ, chạy các script, cài đặt phần mềm chính xác, trong khi một công cụ cấp cao (như apt-get, yum, hoặc
 zypper) làm việc với các nhóm gói, tải các gói từ nhà cung cấp, và tính toán các sự phụ thuộc. Việc cài đặt bằng công cụ cấp thấp
 khá khó khăn do có nhiều dependencies phụ thuộc nhau. Tôi sẽ trình bày 1 trải nghiệm của bản thân khi cài phần mềm nmap bằng tool dpkg.
 
 
Lỗi Unmet dependencies.
Sau khi down gói nmap về. dùng lệnh dpkg -i không được, cài g++ thì bị lỗi này.

![anhloi](https://i.imgur.com/7kL2ETY.png)


Đầu tiên phải loại bỏ lỗi trên bằng cách dùng lệnh:

```
apt-get autoclean
apt-get -f install
dpkg --configure -a
apt-get -f install
apt-get -u dist-upgrade
```

Sau bước trên ta cài đặt bằng cách dùng 1 máy có internet làm máy download file dependency cần thiết. để cài phần mềm (nmap) 
Chuyển các file “.deb” trong /var/cache/apt/archives sang 1 thư mục khác.
Cài đặt: apt-get install nmap
scp các file trong /var/cache/apt/archives để cài cho các máy offline
Cài các gói được yêu cầu bằng lệnh dpkg –i tên_gói


Để xóa phần mềm ta dùng lệnh `sudo apt-get remove nmap`
Nếu xóa toàn bộ dependency `sudo apt-get remove --auto-remove nmap`
Loại bỏ config/data luôn thì ta dùng option purge thay remove

**Các lệnh quản lý gói**

| Hành động                               | RPM                                  | Deb                    |
|-----------------------------------------|--------------------------------------|------------------------|
| Cài đặt gói                             | rpm –i foo.rpm                       | dpkg --install foo.de  |
| Cài đặt gói với dependencies trong repo | yum install foo                      | apt-get install foo    |
| Xóa gói                                 | rpm–e foo.rpm                        | dpkg --remove foo.deb  |
| Xóa gói và dependencies dùng repo       | yum remove foo                       | apt-get remove foo     |
| Update gói                              | rpm –U foo.rpmdpkg --install foo.deb | dpkg --install foo.deb |
| Update gói dùng repo                    | yum update foo                       | apt-get upgrade foo    |
| Update toàn bộ hệ thống                 | yum update                           | apt-get dist-upgrade   |
| Hiện toàn bộ các gói đã cài đặt         | yum list installed                   | dpkg --list            |
| Lấy thông tin về gói đã cài đặt         | rpm –qil foo                         | dpkg --listfiles foo   |
| Hiện các thông tin về gói đang có       | yum list foo                         | apt-cache search foo   |
| Hiện tất cả các gói có thể              | yum list                             | apt-cache dumpavail    |

[thamkhao](https://askubuntu.com/questions/408608/saving-a-apt-get-file-for-future-installations-how-do-i-do-it)
