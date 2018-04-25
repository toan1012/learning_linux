# Cài đặt ZABBIX 3.4 trên Centos 7


## Phần 1: Cài apache2

Để đảm bảo việc cài đặt không bị hỏi tới quyền thì ta dùng user root để cài bằng cách gõ 

`su -` rồi đánh mật khẩu. 

Install httpd.

`# yum -y install httpd`

Xóa bỏ màn hình welcome

`# rm -f /etc/httpd/conf.d/welcome.conf`

Cấu hình apache bằng cách mở file http.conf rồi thay đổi các thông số như dưới.
Các thông số email addmin hay server name sẽ thay đổi tùy thuộc vào cấu hình hiện tại của máy.

```
[root@www ~]# vi /etc/httpd/conf/httpd.conf
# line 86: change to admin's email address

ServerAdmin 
# line 95: change to your server's name

ServerName www.srv.world:80
# line 151: change

AllowOverride All
# line 164: add file name that it can access only with directory's name

DirectoryIndex index.html index.cgi index.php
# add follows to the end

# server's response header

ServerTokens Prod
# keepalive is ON

KeepAlive On
```

Lưu file rồi khởi động apache, tiếp theo đó enable để đảm bảo khỏi động cùng hệ thống sau khi reboot,
Để làm vậy ta dùng lệnh `enable`, Tuy nhiên, chưa có gì đảm bảo là hệ thống có thể chạy được vì mặc định
tường lửa (chương trình firewalld trên centos 7) sẽ chặn cổng 80) vậy nên cần mở rule cho cổng 80.
Những phần tiếp theo sẽ không giải thích nhiều về những lệnh này. Chú ý, *mỗi khi tác động tới file cấu hình nên 
back up*, trong phần trình bày tôi sẽ không nói rõ file nào phải backup vì như vậy, nhìn sẽ rất dài. Một điều nữa
cần lưu ý, bất kỳ thay đổi nào về cấu hình để chắc chắn cần phải được restart lại dịch vụ tương ứng. Ví dụ sửa file
cấu hình ssh thì cần restart lại dịch vụ sshd, sửa file cấu hình apache phải restart lại apache.

```
# systemctl start httpd
# systemctl enable httpd
# firewall-cmd --add-service=http --permanent
success
# firewall-cmd --reload
success
```

Sau khi làm những thao tác trên, ta đã cài xong apache. việc tiếp theo là test hoạt động bằng cách tạo ra 1 file html và
chạy trên trình duyệt như dưới.

```
# vi /var/www/html/index.html

<html>
<body>
<div style="width: 100%; font-size: 40px; font-weight: bold; text-align: center;">
This is my page 
</div>
</body>
</html>
```

Gõ đường link của server trên trình duyệt để thấy dòng "This is my page " hiện ra

!([Imgur](https://i.imgur.com/Twnw56N.png))

## Phần 2: Cài đặt PHP

Install PHP.

`# yum -y install php php-mbstring php-pear`

Chỉnh sửa timezone theo như hướng dẫn bên dưới

`# vi +878 /etc/php.ini`

Sau khi mở file php.ini tại dòng 878 ta sửa như dưới.

```
# line 878: uncomment and add your timezone

date.timezone = "Asia/Ho_Chi_Minh"
```

Save file và khởi động lại apache,

`# systemctl restart httpd`

Tạo file index.php để test, chương trình sẽ hiện ra thông tin về thời gian.

```
# vi /var/www/html/index.php

<html>
<body>
<div style="width: 100%; font-size: 40px; font-weight: bold; text-align: center;">
<?php
   print Date("Y/m/d");
?>
</div>
</body>
</html>
```

![Imgur](https://i.imgur.com/CRw6cbe.png)

## Phần 3: Cài đặt Mariadb

ZABBIX có thể dùng được với nhiều hệ CSDL nhưng phổ biến là họ Mysql.

Mariadb là hệ quản trị CSDL gần giống với Mysql. 

Phía dưới là quy trình vài Mariadb cho zabbix. Tóm tắt toàn bộ quá trình cài đặt gồm: Cài đặt gói phần mềm 
Mariadb sau đó tạo database và user để cài đặt zabbix, phân quyền cho user, dump gói DB cấu hình và khởi động. Cụ thể như sau

Cài gói mariadb-server và cấu hình char set.

```
# yum -y install mariadb-server
# vi /etc/my.cnf

thêm character-set-server vào nhóm mysqld

[mysqld]
character-set-server=utf8
# systemctl start mariadb
# systemctl enable mariadb

ln -s '/usr/lib/systemd/system/mariadb.service' '/etc/systemd/system/multi-user.target.wants/mariadb.service'
[2] 	Initial Settings for MariaDB.
```

Cái gói secure cho MariaDB.

```
[root@localhost ~]# mysql_secure_installation


NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

# cài đặt mật khẩu cho root

Set root password? [Y/n] y

New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!

By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

# xóa user annonymous

Remove anonymous users? [Y/n] y

 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

# Không cho phép tài khoản root điều khiển 

Disallow root login remotely? [Y/n] y

 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

# Xóa CSDL test

Remove test database and access to it? [Y/n] y

 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

# reload privilege tables

Reload privilege tables now? [Y/n] y

 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!

# Kết nối MariaDB với root

[root@localhost ~]# mysql -u root -p

Enter password:     # MariaDB root password you set

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 5.5.37-MariaDB MariaDB Server

Copyright (c) 2000, 2014, Oracle, Monty Program Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

# show user list

MariaDB [(none)]> select user,host,password from mysql.user;

+------+-----------+-------------------------------------------+
| user | host      | password                                  |
+------+-----------+-------------------------------------------+
| root | localhost | *xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx |
| root | 127.0.0.1 | *xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx |
| root | ::1       | *xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx |
+------+-----------+-------------------------------------------+
3 rows in set (0.00 sec)

# show database list

MariaDB [(none)]> show databases;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)

MariaDB [(none)]> exit

Bye
[3] 	If Firewalld is running and also MariaDB is used from remote Hosts, allow service like follows. MariaDB uses 3306/TCP.
[root@www ~]# firewall-cmd --add-service=mysql --permanent

success
[root@www ~]# firewall-cmd --reload

success 
```




