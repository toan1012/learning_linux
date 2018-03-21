# Thao tác với file và thư mục

Trong phần này ta sẽ tìm hiểu các thao tác liên quan tới file như: tạo, sửa, xóa, tìm kiếm, di chuyển file và thư mục

## 1. Tạo file và thư mục

### Tạo file
*  `touch file_name [filename2,...]`

Tạo file theo cách này file sẽ rỗng, có thể tạo nhiều file. Có thể tạo 1 file ở thư mục khác bằng cách thêm đường dẫn tới thư mục phái trước file_name.

* dùng trình soạn thảo bất kì như vi, vim, nano...
Cách này có thể thêm nội dung vào luôn. Đây cũng là cách để sửa file.

### Tạo thư mục
* `mkdir /path/to/dir`

Tất nhiên, tạo file hay thư mục rồi sẽ phải quan tâm tới quyền là sở hữu của chúng. Điều này sẽ được nói trong bài sau. 

### 2. Xóa file và thư mục.
* `rm -rf /path/to/dir` xóa không hỏi toàn bộ thư mục.
* `rm -rf /path/to/dir/` xóa toàn bộ subdir và file của dir để lại thư mục dir rỗng.
* nếu thay option -f bằng -i thì hệ thống sẽ hỏi trước khi xóa.
* hoàn toàn tương tự với file, chỉ cần thay đường dẫn vào lệnh trên.

### 3. Tìm kiếm
Đây là thao tác thường xuyên khi làm việc với file, thư mục. ở đây cũng chỉ nói về lệnh thường dùng là find, kết hợp với grep. 

Trong thực tế, không phải lúc nào ta cũng nhớ được toàn bộ nội dung file, có thể chỉ là 1 vài ký tự, phần mở rộng, nhóm ký tự liền kề. Trong tình huống như vậy, cần có các ký tự đại diện để cụ thể hóa những gì ta đã biết nhằm thu hẹp phạm vi tìm kiếm. 
Các lí tự đó gọi chung là __wildcard__.

| Kí tự   |      Ý nghĩa      | 
|----------|:-------------:|
| ? |    Tương ứng với 1 ký tự     |
| * |    Phù hợp với bất kỳ chuỗi ký tự    |
| [set] | Phù hợp bất kỳ ký tự nào có trong tập hợp các ký tự  | 
| [!set] | Phù hợp bất kỳ ký tự nào có trong tập hợp các ký tự  |


Lệnh cơ bản để tìm kiếm thư mục và file đó là lệnh find. Dưới đây là một số cách thực hiện lệnh find

### 3.1. Đưa danh sách các file và thư mục con của thư mục hiện tại

```
toan@toan:~/program/www/layer3$ find


.
./f3.txt
./error.txt
./dongvatcovu
./dongvatcovu/dog.txt
./concho.txt
./dongvatbietbay
./f4.txt
./konmeo1.txt
./dongvatcovu.md
./conmeo.txt
toan@toan:~/program/www/layer3$

```

Lệnh trên tương đương với 2 lệnh:


`$ find .`

`$ find . -print`


### 3.2. Tìm kiếm trong thư mục hoặc đường dẫn

```
toan@toan:~$ find /home/toan/program/
/home/toan/program/
/home/toan/program/www
/home/toan/program/www/layer3
/home/toan/program/www/layer3/f3.txt
/home/toan/program/www/layer3/error.txt
/home/toan/program/www/layer3/dongvatcovu
/home/toan/program/www/layer3/dongvatcovu/dog.txt
/home/toan/program/www/layer3/concho.txt
/home/toan/program/www/layer3/dongvatbietbay
/home/toan/program/www/layer3/f4.txt
/home/toan/program/www/layer3/konmeo1.txt
/home/toan/program/www/layer3/dongvatcovu.md
/home/toan/program/www/layer3/conmeo.txt
```

### 3.3. Tìm kiếm các file theo tên
Sử dụng option -name
Thêm nữa ta cũng dùng được wildcard trong việc tìm kiếm.

```
toan@toan:~/program/www/layer3$ find . -name *.md
./dongvatcovu.md
```

Việc tìm kiếm theo kiểu đệ quy. Khi search từ "/" thì sẽ tìm kiếm trong toàn bộ hệ thống file bao gồm cả thiết bị đã mount và mạng thiết bị lưu trữ
Nếu không để ý tới tên file viết hoa thường ta dùng thêm option -iname ví dụ như:

```
$ find ./test -iname *.Php
./test/subdir/how.php
./test/cool.php
```

### 3.4. Giới hạn độ sâu cây thư mục tìm kiếm
Giới hạn độ sâu tính từ vị trí của thư mục bắt đầu tìm kiếm. 

```
toan@toan:~/program/www/layer3$ find . -maxdepth 2 -name "*.txt"
./f3.txt
./error.txt
./dongvatcovu/dog.txt
./concho.txt
./f4.txt
./konmeo1.txt
./conmeo.txt
```

### 3.5. Tìm kiếm file, thư mục kiểu không phù hợp với tên hoặc pattern.

```
toan@toan:~/program/www/layer3$ find . -maxdepth 2 -not -name "*.txt"
.
./dongvatcovu
./dongvatbietbay
./dongvatcovu.md
```

Phía trên ta đang tìm kiếm các file có độ sâu thư mục không quá 2 và không có đuôi txt
Thay vì thêm -not ta có thể dùng ký hiệu ! . Lệnh trên sẽ được viết lại là 
find . -maxdepth 2 ! -name "*.txt"

### 3.6. Gộp nhiều điều kiện tìm kiếm 

```
$ find ./test -name 'abc*' ! -name '*.php'
./test/abc.txt
./test/abc
```
Đoạn trên tìm kiếm file, thư mục bắt đầu bằng abc và không có đuôi là php.

__Toán hạng OR__ dùng để kết nối 2 điều kiện theo kiểu hoặc.

```
$ find -name '*.php' -o -name '*.txt'
./abc.txt
./subdir/how.php
./abc.php
./cool.php
```

Tìm kiếm file có đuôi php hoặc txt

### 3.7. Search chỉ file hoặc thư mục
Nhờ tùy chọn -type f để tìm chỉ file, -type d cho kiểu tìm chỉ thư mục.

```
$ find ./test -type f -name abc*
./test/abc.txt
```

Chỉ tìm thư mục

```
$ find ./test -type d -name abc*
./test/abc
```

### 3.8. Search trong những thư mục khác nhau

2 thư mục ./test và ./dir sẽ tìm kiếm đồng thời

```
$ find ./test ./dir2 -type f -name abc*
./test/abc.txt
./dir2/abcdefg.txt
Check, that it listed files from 2 separate directories.
```

### 3.9. Tìm kiếm file ẩn

`$ find ~ -type f -name ".*"`

### 3.10. Tìm kiếm file dựa vào permission

```
$ find . -type f -perm 0664
./abc.txt
./subdir/how.php
./abc.php
./cool.php
```

### 3.11. Tìm kiếm file với sgid/suid bits 

Tùy chọn "perm" trong lệnh find cho phép tìm kiếm tất cả các file có quyền 644 (hoặc một giá trị nào khác và kèm 
theo giá trị sgid bit (sgid, sticky bit).

`# find / -perm 2644`

Tương tự 1664 cho sticky bit. Tùy chọn perm cũng cho phép hỗ trợ sử dụng ký tự thay vì số dạng thập phân.

```$ find / -maxdepth 2 -perm /u=s 2>/dev/null
/bin/mount
/bin/su
/bin/ping6
/bin/fusermount
/bin/ping
/bin/umount
/sbin/mount.ecryptfs_private
```

Chú ý "2>/dev/null" Không hiện lỗi ra

### 3.12. Tìm file chỉ đọc.

Thực chất vẫn dùng tùy chọn perm với việc tìm file có quyền /u=r

```
$ find /etc -maxdepth 1 -perm /u=r
/etc
/etc/thunderbird
/etc/brltty
/etc/dkms
/etc/phpmyadmin
... output truncated ...
```

### 3.13. Tìm kiếm file có khả năng thực thi

```
$ find /bin -maxdepth 2 -perm /a=x
/bin
/bin/preseed_command
/bin/mount
/bin/zfgrep
/bin/tempfile
```

### 3.14. Tìm file thuộc về một user cụ thể

Ví dụ dưới ta tìm các file trong thư mục ./program/www/layer3 của user toan

```
toan@toan:~$ find ./program/www/layer3/ -user toan
./program/www/layer3/
./program/www/layer3/f3.txt
./program/www/layer3/error.txt
./program/www/layer3/dongvatcovu
./program/www/layer3/dongvatcovu/dog.txt
./program/www/layer3/concho.txt
./program/www/layer3/dongvatbietbay
./program/www/layer3/conmeohen.md
./program/www/layer3/f4.txt
./program/www/layer3/konmeo1.txt
./program/www/layer3/dongvatcovu.md
./program/www/layer3/conmeo.txt
```

### 3.15. Tìm file đã được modified trong thời gian cụ thể

Lệnh dưới cho phép tìm tất cả file trên đã bị thay đổi từ 50 ngày trước.

`# find / -mtime 50`

### 3.16. Tìm files được quản lý trong N ngày gần đây.

`# find / -atime 50`

### 3.17. Tìm file đã thay đổi trong khoảng thời gian

Tìm tất cả các file đã thay đổi trong khoảng 50 tới dưới 100 ngày trở lại đây.

`# find / -mtime +50 –mtime -100`

### 3.18. Tìm file có kích thước thỏa mãn

To find all 50MB files, use.

`# find / -size 50M`

Tìm file có kích thước lớn hơn 50MB và nhỏ hơn 100MB.

`$ find / -size +50M -size -100M`

Tìm file trống.

`# find /tmp -type f -empty`

Tìm thư mục trống.

`$ find ~/ -type d -empty


### 3.19. Xóa file hoặc thư mục sau khi tìm được

`$ find /tmp -type f -name "*.txt" -exec rm -f {} \;`

Với thư mục ta thay type f bằng type d.

`$ find /home/bob/dir -type f -name *.log -size +10M -exec rm -f {} \;`

Đoạn script trên xóa toàn bộ các file .log có kích thước 10MB


 