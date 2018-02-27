#Thao tác với file và thư mục

Trong phần này ta sẽ tìm hiểu các thao tác liên quan tới file như: tạo, sửa, xóa, tìm kiếm, di chuyển file và thư mục

##1. Tạo file và thư mục

###Tạo file
*  `touch file_name [filename2,...]`

Tạo file theo cách này file sẽ rỗng, có thể tạo nhiều file. Có thể tạo 1 file ở thư mục khác bằng cách thêm đường dẫn tới thư mục phái trước file_name.

* dùng trình soạn thảo bất kì như vi, vim, nano...
Cách này có thể thêm nội dung vào luôn. Đây cũng là cách để sửa file.

###Tạo thư mục
* `mkdir /path/to/dir`

Tất nhiên, tạo file hay thư mục rồi sẽ phải quan tâm tới quyền là sở hữu của chúng. Điều này sẽ được nói trong bài sau. 

###2. Xóa file và thư mục.
* `rm -rf /path/to/dir` xóa không hỏi toàn bộ thư mục.
* `rm -rf /path/to/dir/` xóa toàn bộ subdir và file của dir để lại thư mục dir rỗng.
* nếu thay option -f bằng -i thì hệ thống sẽ hỏi trước khi xóa.
* hoàn toàn tương tự với file, chỉ cần thay đường dẫn vào lệnh trên.

###3. Tìm kiếm
Đây là thao tác thường xuyên khi làm việc với file, thư mục. ở đây cũng chỉ nói về lệnh thường dùng là find, kết hợp với grep. 

Trong thực tế, không phải lúc nào ta cũng nhớ được toàn bộ nội dung file, có thể chỉ là 1 vài ký tự, phần mở rộng, nhóm ký tự liền kề. Trong tình huống như vậy, cần có các ký tự đại diện để cụ thể hóa những gì ta đã biết nhằm thu hẹp phạm vi tìm kiếm. 
Các lí tự đó gọi chung là __wildcard__.



| Kí tự | Ý nghĩa |
|-------|---------|
|       |         |
|       |         |
|       |         |


| Tables   |      Are      |  Cool |
|----------|:-------------:|------:|
| col 1 is |  left-aligned | $1600 |
| col 2 is |    centered   |   $12 |
| col 3 is | right-aligned |    $1 |
 