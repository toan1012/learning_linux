# Hướng dẫn cài đặt MySQL 

Điều kiện ban đầu: là máy đã cài ubuntu 16.04LTS, user có quyền sudo 

## Step1-Thêm repository chứa Mysql

Nhà phát triển MySQL đưa ra 1 package .deb dùng để xử lý việc cấu hình và cài đặt MySQL từ 
repositories. Khi repo được setup sẽ cho phép ta cài đặt bằng lệnh apt-get. Đầu tiên, tải file 
.deb nói trên bằng lệnh curl sau đó cài bằng lệnh dpkg.

Trước tiên, tải file .deb theo link https://dev.mysql.com/downloads/repo/apt/ tìm tới nút Download
ở dưới bên phả, bỏ qua bước login click vào "No thanks, just start my download.", Một file 
được tải về

Mở mobaxterm, dùng ftp đẩy file mysql-apt-config_0.8.9-1_all.deb lên VM đang cài. ở đây, 
file tải lên đặt ở thư mục /home/toan.

Download file sử dụng lệnh dưới, file down về để tại thư mục hiện tại. 

`curl -OL https://dev.mysql.com/get/mysql-apt-config_0.8.3-1_all.deb`

Sẵn sàng cài đặt 

`sudo dpkg -i mysql-apt-config*`

Màn hình hiện chon version MySQL cần cài. Chọn Ok dòng thứ 4 trước khi chọn OK ở dòng cuối. Bước
này dùng để lựa chọn bản MySQL (giả sử muốn cài cluster DB sẽ phải chọn ở bước này.

![Imgur](https://i.imgur.com/aIU3uBb.png)

Để kết thúc việc cài đặt ta update package và xóa file .deb tải về nếu thấy không cần lưu trữ


## Step 2- Cài đặt MySQL

`sudo apt-get install mysql-server`

Kiểm tra trạng thái mysql bằng lệnh `systemctl status mysql` cho kết quả

```
toan@dhcpserver:~$ systemctl status mysql
● mysql.service - MySQL Community Server
   Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: en
   Active: active (running) since Thu 2018-03-29 00:27:56 ICT; 41s ago
 Main PID: 6663 (mysqld)
   CGroup: /system.slice/mysql.service
           └─6663 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld

Mar 29 00:27:56 dhcpserver systemd[1]: Starting MySQL Community Server...
Mar 29 00:27:56 dhcpserver systemd[1]: Started MySQL Community Server.
```

Kết quả "Active: active (running)" thì OK.

Nếu muốn tăng sự an toàn về tài khoản mysql ta có thể cài `mysql_secure_installation` 
và làm theo hướng dẫn trong từng bước để đảm bảo các điều kiện về mật khẩu, quyền, 
xóa CSDL test.

## Step 3- Testing MySQL

Sử dụng lệnh `mysqladmin -u root -p version` để login vào MySQL dưới quyền root và hỏi passwd
rồi in ra thông tin về version, uptime, status của mysql. Ví dụ như dưới đây.

```
toan@dhcpserver:~$ mysqladmin -u root -p version
Enter password:
mysqladmin  Ver 8.42 Distrib 5.7.21, for Linux on x86_64
Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version          5.7.21
Protocol version        10
Connection              Localhost via UNIX socket
UNIX socket             /var/run/mysqld/mysqld.sock
Uptime:                 5 min 46 sec

Threads: 1  Questions: 12  Slow queries: 0  Opens: 113  Flush tables: 1  Open tables: 106  Queries per second avg: 0.034
```

Tham khảo

[https://www.digitalocean.com/community/tutorials/how-to-install-the-latest-mysql-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-install-the-latest-mysql-on-ubuntu-16-04)


