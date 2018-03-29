# Thao tác tạo user, cấp quyền, và tạo DB

Mục tiêu: Tạo được DB và tạo các bảng trong đó rồi đổ dữ liệu vào
để test. database có tên testdb và user mysql quản lý tên là testuser. 

Trong quá trình thao tác có 1 số lỗi gặp phải sẽ được liệt kê chi tiết trong phần 
"Bàn chuyện ngoài lề".

## Vào DB

```
$ mysql -u USERNAME -h HOSTNAME -p
$ mysql -u root -p
```

## Create DB và user
Tạo DB và user xong thì cần grant quyền cho user, để xác định user có quyền
như thế nào đối với database. Phần grant sẽ được nói vào 1 mục riêng. Mục
tiêu hiện tại là tạo được DB và tạo các bảng trong đó 

```
create database testdb;
create user 'testuser'@'localhost' identified by 'Caidat@223';
grant all on testdb.* to 'testuser'@'localhost';
flush privileges;
```

hoặc 

```
create database testdb;
grant all on testdb.* to 'testuser' identified by 'Caidat@223';
flush privileges;
```

Tiếp theo exit và vào bằng user test `mysql -u testuser -p;` nhập mật khẩu.

Sử dụng lệnh `show databases;` để nhìn các databases.

Tiếp theo `use testdb;` để chọn database tác động

Tạo 1 bảng demo với tên task bằng lệnh dưới đây.

```
CREATE TABLE IF NOT EXISTS tasks (
  task_id INT(11) NOT NULL AUTO_INCREMENT,
  subject VARCHAR(45) DEFAULT NULL,
  start_date DATE DEFAULT NULL,
  end_date DATE DEFAULT NULL,
  description VARCHAR(200) DEFAULT NULL,
  PRIMARY KEY (task_id)
);
```

Kiểm tra lại bằng lệnh `show tables;`, nếu có table task ta kiểm tra tiếp

Kiểm tra mô tả của bảng bằng lệnh `describe tasks;`

## Đổi password và chính sách mật khẩu của mysql

Nếu để ý ta thấy trong phần cài đặt mysql có phần mở rộng là `mysql_secure_installation`
để cài các chính sách bảo mật. Tôi đã chọn mức bảo mật medium.
Trong khi đổi mật khẩu tôi đã sai chính sách với việc đặt mật khẩu chỉ có 6 ký tự, vi phạm
chính sách độ dài ít nhất 8 ký tự. Để xem chính sách đã chọn ta xem bằng lệnh

`show variables like 'validate_password%';`. Lệnh thay đổi mật khẩu đó như thế này.

`alter USER 'testuser'@'localhost' IDENTIFIED BY 'Caidat';`

Nhớ dấu ";" ở cuối mỗi lệnh. 

```
+--------------------------------------+--------+
| Variable_name                        | Value  |
+--------------------------------------+--------+
| validate_password_check_user_name    | OFF    |
| validate_password_dictionary_file    |        |
| validate_password_length             | 8      |
| validate_password_mixed_case_count   | 1      |
| validate_password_number_count       | 1      |
| validate_password_policy             | MEDIUM |
| validate_password_special_char_count | 1      |
+--------------------------------------+--------+
```

Ta có thể thay đổi các giá trị trong bảng bằng lệnh `SET global variable_name = giá trị`.
 Giả sử tôi sẽ set validate_password_length ít nhất 6 ký tự và validate_password_length
phải từ 2 trở lên. 

```
mysql> set global validate_password_length = 6;
Query OK, 0 rows affected (0.00 sec)

mysql> set global validate_password_number_count = 2;
Query OK, 0 rows affected (0.00 sec)

mysql> show variables like 'validate_password%';
+--------------------------------------+--------+
| Variable_name                        | Value  |
+--------------------------------------+--------+
| validate_password_check_user_name    | OFF    |
| validate_password_dictionary_file    |        |
| validate_password_length             | 6      |
| validate_password_mixed_case_count   | 1      |
| validate_password_number_count       | 2      |
| validate_password_policy             | MEDIUM |
| validate_password_special_char_count | 1      |
+--------------------------------------+--------+
7 rows in set (0.00 sec)
```
Sau khi rút kinh nghiệm đặt lại:

`alter USER 'testuser'@'localhost' IDENTIFIED BY '**idat@22*'`

Phần tìm hiểu về lệnh SET sẽ dành vào dịp khác. 

Ai lười và không cần bảo mật có thể xóa flugin secure đi bằng lệnh `uninstall plugin validate_password;`

Tuy nhiên lệnh này tôi chưa thử. 

## Phải kiểm tra những gì đã tạo

Khi tạo user hay database hay sau này là record, attribute đêu phải test. 

Trong bài khởi đầu này, tôi sẽ test user vừa tạo bằng cách select vào bảng mysql.user

mặc định của mysql

```
mysql> select user from mysql.user;
+---------------+
| user          |
+---------------+
| mysql.session |
| mysql.sys     |
| root          |
| testuser      |
+---------------+
4 rows in set (0.00 sec)
```

Tiếp theo kiểm tra Database testdb vừa tạo bằng lệnh `show databases;`.

Nếu muốn kiểm tra bảng trong database dùng `show tables;` 

Kiểm tra column dùng `show columns from mytable;`

Nếu muốn xem mô tả về bảng dùng lệnh `describe pet`.
 
Kiểm tra user có những quyền gì ta dùng `show grants for user`;

```
mysql> show grants for testuser@'localhost';
+--------------------------------------------------------------+
| Grants for testuser@localhost                                |
+--------------------------------------------------------------+
| GRANT USAGE ON *.* TO 'testuser'@'localhost'                 |
| GRANT ALL PRIVILEGES ON `testdb`.* TO 'testuser'@'localhost' |
+--------------------------------------------------------------+
2 rows in set (0.00 sec)
```

Kiểm tra 1 DB thuộc những user nào quản lý. --------------------------------------------------------

## Phân quyền user

Các việc cần phải làm cho mục này là thêm user, xóa user, thêm quyền cho user, bớt quyền cho user. 

* Create Mysql user

`CREATE USER 'testuser'@'localhost' IDENTIFIED BY 'Caidat1!';`

Tạo user tên là testuser lại localhost có password là Caidat1!

* Drop mysql user

`DROP USER 'NAME'@'ip';` Nếu user đang remote sẽ không xóa được và báo lỗi 1396.

* Phân quyền cho user.

```
GRANT permission ON database.table TO 'user'@'localhost';
FLUSH PRIVILEGES;
```

Trong đó permission có thể là.

    * ALL – Có tất cả quyền bên dưới.
    * CREATE – Cho phép một user tạo DB và tables
    * DELETE – Cho phép một user xóa hàng trong bảng
    * DROP – Cho phép một user bỏ database và bảng
    * EXECUTE – Cho phép một user thực thi việc lưu trữ
    * GRANT OPTION – Cho phép một user cấp quyền hay xóa quyền của user khác
    * INSERT – Cho phép một user thêm hàng vào bảng
    * SELECT – Cho phép một user select dữ liệu từ 1 bảng
    * SHOW DATABASES- Cho phép một user xem list database
    * UPDATE – Cho phép một user update hàng trong bảng

Thực tế `GRANT CREATE ON *.* TO 'testuser'@'localhost';`

Dấu * đại diện cho tất cả khả năng xảy ra.

Sau khi xong dùng lệnh `FLUSH PRIVILEGES;` để làm quyền thay đổi. giống như save vậy. 

* Giảm bớt quyền của user

```
REVOKE permission ON database.table FROM 'user'@'localhost';
FLUSH PRIVILEGES;
```

Ví dụ:

```
REVOKE CREATE ON *.* FROM 'testuser'@'localhost';
FLUSH PRIVILEGES;
```

permission trong lệnh trên tương tự của permission phần cấp quyền.

	
## Binding address

Nếu ai đó trong cùng mạng LAN(giới hạn hiện giờ là trong mạng Lan) muốn truy cập vào DB mà không
dùng SSH thì ta sử dụng cách binding-address để cho phép máy đó remote qua cổng 3306 (thông
tin này đọc được qua stackoverflow cần được kiểm chứng). 

Điều kiện lab: 2 máy đều chạy mysql 5.7, máy bị remote đang chạy Ubuntu 16.04LTS

**B1. Sửa file cấu hình mysql**

* Trên máy bị remote vào sửa file: /etc/mysql/mysql.conf.d/mysqld.cnf

`vi /etc/mysql/mysql.conf.d/mysqld.cnf`

* Nhìn thuộc tính binding-address điền vào IP của máy remote, trong trường hợp của mình, tôi 
đang điền 0.0.0.0 để cho phép bất kỳ máy nào remote. Mỗi 1 IP 1 dòng. Có 10 máy remote có 
10 dòng. Save.

**B2. Khởi động lại dịch vụ**

`service mysql restart`

**B3. Cấp quyền cho user remote**
Để remote có thể điều khiển được phải cấp quyền 

Để cấp quyền cần vào bằng user root `mysql -uroot -p`

`GRANT ALL ON <local database name>.* TO <remote web node user name>@<remote web node server ip address> 
IDENTIFIED BY '<database user password`

Cụ thể

`grant all *.* to toandm@'%' identified by 'Caidat@223';`

Câu lệnh với ý nghĩa, cấp tất cả các quyền cho user toandm có password là "Caidat@223" từ 
bất kỳ IP remote nào đều có thể truy cập vào tất cả các databases trên máy bị remote.

Sau câu lệnh trên trong bảng mysql.user của máy bị remote  

Thực tế, ta phải giới hạn quyền rất sát cho từng user để tránh lỗ hổng, đặc biệt không được sử dụng user
root của Mysql để vào DB. Nhớ rằng các account ứng dụng, DB, OS khác nhau. Trong bước trên 
dùng account OS của máy remote để vào DB nhưng thực chất là nó đã trở thành account DB
Để chứng minh, trên máy bị remote ta gõ user os vào và kết quả như dưới

```
toan@dhcpserver:~$ mysql -u toan -p
Enter password:
ERROR 1045 (28000): Access denied for user 'toan'@'localhost' (using password: YES)
```

## Tham khảo

[http://devdocs.magento.com/guides/v2.0/install-gde/prereq/mysql_remote.html](http://devdocs.magento.com/guides/v2.0/install-gde/prereq/mysql_remote.html)

[https://dev.mysql.com/doc/refman/5.7/en/drop-user.html](https://dev.mysql.com/doc/refman/5.7/en/drop-user.html)

[https://dev.mysql.com/doc/refman/5.5/en/getting-information.html](https://dev.mysql.com/doc/refman/5.5/en/getting-information.html)

[https://dev.mysql.com/doc/refman/5.7/en/set-password.html](https://dev.mysql.com/doc/refman/5.7/en/set-password.html)


