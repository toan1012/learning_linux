# Cài đặt wordpress

WordPress là mã nguồn mở được viết bằng PHP giúp ta dễ dàng quản trị nội dung (CMS-content manager system) của 1 website, wordpress là back-end được dùng rất phổ biến.

Ta sẽ cài wordpress trên 1 máy ubuntu 16.04

## Bước 1: Install Apache Web Server

`$ sudo apt-get install apache2 apache2-utils `

Chúng ta cần enable apache và set cho nó luôn khởi động cùng sau mỗi lần boot.

```
$ sudo systemctl enable apache2
$ sudo systemctl start apache2
```

Để test apache đã hoạt động chưa, ta mở trình duyệt lên và gõ http://server_address. Nếu có 1 trang apache default xuất hiện thì đúng

![Imgur](https://i.imgur.com/EiIAk2D.png)

**Chú ý**: Thư mục root mặc định của apache là /var/www/html.

## Bước 2: Cài đặt MySQL Database Server

`$ sudo apt-get install mysql-client mysql-server`

Trong khi cài đặt gói, Ta sẽ set password cho user root. 

![Imgur](https://i.imgur.com/uxT0vro.png)

Database server vẫn chưa được an toàn. Cài đặt gói `mysql_secure_installation `

`$ sudo mysql_secure_installation `

Đầu tiên, ta sẽ bị hỏi ‘validate_password’ plugin, gõ Y/Yes và Enter, chọn default password length. 

Nếu không muốn thay đổi root password, gõ N/No để thực hiện tiếp.

## Step 3: Install PHP and Modules

`$ sudo apt-get install php7.0 php7.0-mysql libapache2-mod-php7.0 php7.0-cli php7.0-cgi php7.0-gd`  

Để test php ta tạo file info.php file inside /var/www/html.

`$ sudo vi /var/www/html/info.php`

Sau đó paster những dòng dưới vào, thực chất nó chỉ là 1 hàm của php để lấy thông tin phiên bản.

```
<?php 
phpinfo();
?>
```

Khi xong bước trên, lưu lại rồi vào web browser gõ http://server_address/info.php. Thấy thông tin như hinh là được

![Imgur](https://i.imgur.com/no7rtlc.png)

## Step 4: Install WordPress CMS

Download phiên bản mới nhất về. và giải nén

```
$ wget -c http://wordpress.org/latest.tar.gz
$ tar -xzvf latest.tar.gz
```

Di chuyển các file về thư mục default, /var/www/html/:

`$ sudo rsync -av wordpress/* /var/www/html/`

Set đúng permission và ownship 

```
$ sudo chown -R www-data:www-data /var/www/html/
$ sudo chmod -R 755 /var/www/html/
```

Step 5: Create WordPress Database

`$ mysql -u root -p `

Ở mysql shell, gõ theo những lệnh dưới. Trong đó `wp_myblog` là tên database `your_username_here` là tên user vào database, 
`your_chosen_password_here` là password đã set từ trước.

```
mysql> CREATE DATABASE wp_myblog;
mysql> GRANT ALL PRIVILEGES ON wp_myblog.* TO 'your_username_here'@'localhost' IDENTIFIED BY 'your_chosen_password_here';
mysql> FLUSH PRIVILEGES;
mysql> EXIT;
```

Vào /var/www/html/ rename file wp-config-sample.php sẵn có thành wp-config.php:

$ sudo mv wp-config-sample.php wp-config.php

Thay đổi MySQL settings (refer to the highlighted boxes in the image below):

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'database_name_here'); /** MySQL database username */ define('DB_USER', 'username_here'); /** MySQL database password */ define('DB_PASSWORD', 'password_here'); /** MySQL hostname */ define('DB_HOST', 'localhost'); /** Database Charset to use in creating database tables. */ define('DB_CHARSET', 'utf8'); /** The Database Collate type. Don't change this if in doubt. */ define('DB_COLLATE', '');

restart lại apache và mysql.

```
$ sudo systemctl restart apache2.service 
$ sudo systemctl restart mysql.service 
```

Mở trình duyệt và gõ: http://server-address/wp-admin vào trang quản trị. 

![Imgur](https://i.imgur.com/YjpD5Vc.png)



