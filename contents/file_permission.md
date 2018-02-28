# File permission

Về vấn đề "File permission" ta quan tâm tới 2 điều 
* Sở hữu file tức là file thuộc về  user nào, group nào.
* Quyền thực thi của file: có khả năng đọc, ghi, hay thực thi không.

Để xem được những thông tin trên ta dùng lệnh `ls -l` . Ví dụ: 

```
toan@toan:~/program/www/layer3$ ls -l
total 68
-rw-rw-r-- 1 toan toan     0 Feb 27 16:54 concho.txt
-rw-rw-r-- 1 toan toan     0 Feb 28 01:27 conmeohen.md
-rw-rw-r-- 1 toan root    40 Feb 28 08:31 conmeo.txt
```

Nhóm 10 ký tự bên trái cho biết quyền của file. 

**-rw-rw-r--**

Ký tự đầu tiên đại diện cho kiểu của tập tin: "-" là file, "d" là thư mục,
9 ký tự còn lại chia thành 3 nhóm thuộc quyền của owner-group-other.

Quy đổi sang hệ số 10: quyền read(r) ứng với 4, write(w) ứng với 2, excute(x) ứng với 1. Cộng 
tổng lại theo từng nhóm ta được cách viết gọn. Trong ví dụ trên file concho.txt có quyền 664.
Lệnh trên cũng cho biết file concho.txt thuộc owner:toan, group:toan

**Thay đổi quyền** ta dùng lệnh 

`chmod [quyền theo hệ thập phân hoặc ký hiệu chữ] tên_file`

Ví dụ:

```
chmod 755 conmeo.txt  
chmod u+x conmeo.txt
```

Giá trị 755 tương ứng với rwxr-xr-x.

u+x tức là user thêm quyền thực thi. Dấu + là thêm, dấu - là bớt. 

**Thay đổi quyền sở hữu** dùng lệnh chown

Ví dụ: muốn đổi sở hữu của file concho.txt từ user toan, group toan sang user root, group root ta dùng lệnh
`chown root:root concho.txt`

Để thay đổi group của file hay thư mục ta dùng lệnh chgrp. Ví dụ như muốn đổi group của file concho.txt sang root
`chgrp root concho.txt`

**SUID, SGID, Sticky bit**

**SUID**(Set User ID): Nếu SUID bit được thiết lập cho một ứng dụng, những file có thể thực thi nào đó điều này có
 nghĩa là một người dùng khác không phải là chủ sở hữu của ứng dụng cũng có thể sử chạy như chính chủ sở hữu. Ví dụ:
 
```
toan@toan:~$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 47032 Jan 27  2016 /usr/bin/passwd
```

rws: nghĩa là câu lệnh thay đổi mật khẩu passwd đã được thiết lập SUID bit. Tuy passwd thuộc root nhưng vì đã được 
thiết lập SUID bit nên người dùng khác cũng có thể thực hiện passwd (chỉ có thể thay đổi mật khẩu của chính user thực hiện passwd).

**SGID** (Set Group ID): cũng giống như SUID bit nhưng là dành cho nhóm.

Để thiết lập SUID và SGID bit sử dụng lệnh:
`$ chmod u+s yourfile​`
và:
`$ chmod g+s yourfile​`

**Sticky Bit**: người dùng chỉ có thể xoá những files mà chính họ tạo ra trong thư mục được thiết lập Sticky bit.
 Để bật Sticky bit cho một thư mục chúng ta thực hiện:

 `# chmod +t stickyfolder​`
 
Trong bài trước đây về quyền truy cập và sở hữu tập tin chỉ đề cập đến câu lệnh chmod với 3 giá trị số dạng: 755 hay 644
Tuy nhiên khi bạn muốn thiết lập thêm các bit SUID/SGID và Sticky sử dụng số thì đầy đủ là 4 số dạng: 0755 hay 1644 … 
Số đầu tiên thể hiện SUID/SGID hay Sticky bit có được thiết lập hay không. Ví dụ:

```
$ chmod 0755 testfile
$ chmod 2755 testfile
```

Số đầu tiên cũng nhận giá trị từ 0 đến 7 như 3 số tiếp theo. Bây giờ chỉ xét số đầu tiên vì nó thể hiện SUID, SGID và Sticky.
 Nếu biểu diễn theo hệ nhị phân thì 0 ~ 000, 7 ~ 111 (0 – không được thiết lập, 1 – được thiết lập), do đó dễ dàng có thể biết
 được giá trị nào được đặt, 3 số đó thể hiện lần lượt SUID, SGID và Sticky.
giá trị nhận được là 101 (tương ứng là 5) và từ đó có thể thấy ngay SUID và Sticky được thiết lập còn SGID thì không.