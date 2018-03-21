# Các lệnh linux cơ bản
## Lệnh cd
Lệnh cd giúp ta di chuyển giữa các thư mục. Để di chuyển ta có thể dùng lệnh 

```
cd /path/to/directory
```

Để cho nhanh chóng, linux có các ký tự đặc biệt để di chuyển. Cụ thể như sau:

* "cd ." không di chuyển.
* "cd .." lủi về một cấp trên cây thư mục
* "cd ~" trở về thư mục home của user hiện tại
* "cd /" trở về thư mục gốc.

## Lệnh ls
Lệnh ls dùng để liệt kê các thành phần trong một thư mục hoặc thông tin về file. Liệt kê subdir, file, quyền, sở hữu,...

Các tùy chọn hay sử dụng của ls:

* ls -a hiển thị tất cả các file và thư mục ẩn ở thư mục hiện hành.
* ls -s hiển thị tất cả các thư mục và file ẩn ở thư mục người dùng.
* ls -l hiển thị chi tiết các tập tin và thư mục hiện hành.
* ls -h hiển thị kích thước quy đổi ra Mb hoặc Gb.
* ls -i hiển thị inode của file.

Lệnh ls có thể kết hợp với các ký hiệu đại diện đặc biệt như ., .., ~, / hoặc thêm điều kiện nhờ lệnh grep để liệt kê.

## Hardlink và Softlink
Hai khái niệm này có liên quan mật thiết tới __Inode__.

Từ phía người dùng sẽ nhìn file hoặc thư mục thông qua tên, nhưng đối với hệ thống, file và thư mục là những inode khác nhau có inode number khác nhau lưu trữ những thông tin trong *metadata* của inode.

![inode](http://www.gocit.vn/wp-content/uploads/2013/08/inode.png)

Mỗi inode có 1 inode number duy nhất. Để nhìn được inode number thì ta dùng lệnh ls-l hoặc ls -i 

Thông tin trong inode gồm: 

* Dung lượng file tính bằng bytes.
* Device ID : mã số thiết bị lưu file.
* User ID : mã số chủ nhân của file.
* Group ID.: mã số nhóm của chủ file.
* File mode : gồm kiểu file và các quyền truy cập file (permissions).
* Hệ thống phụ và các cờ hạn chế quyền truy cập file.
* Timestamps: các mốc thời gian khi: bản thân inode bị thay đổi (ctime), nội dung file thay đổi (mtime) và lần truy cập mới nhất (atime).
* Link count : số lượng hard links trỏ đến inode.
* Các con trỏ chỉ đến các blocks trên ổ cứng dùng lưu nội dung file. Theo các con trỏ này mới biết file nằm ở đâu để đọc nội dung.

**Hard link** là một liên kết (link) trỏ đến vị trí lưu một file trên ổ cứng, tức là những file hard link sẽ có cùng số inode.

* Nếu đổi tên, xóa hoặc di chuyển file gốc sang thư mục khác, file hard link vẫn mở được vì nó vẫn trỏ đến vị trí lưu file cố định trên ổ cứng.
Tên hard link có thể khác tên file gốc, hard link có thể nằm trong một thư mục khác với thư mục của file gốc (nhưng không được khác partition). Vì vậy một file có thể có nhiều tên file nằm ở các thư mục khác nhau. 
* Khi sửa ở 1 file có hard link với các file khác, thì tất cả sẽ thay đổi theo khi refresh hoặc reload vì thực chất là sửa trên cùng một vùng nhớ mà tất cả chúng cùng trỏ tới.
* Nếu xóa hard link hoặc xóa tên file gốc nhưng còn một hard link, file vẫn không bị xóa. File chỉ bị xóa khi không còn cái gì trỏ đến vị trí lưu nó. Như vậy muốn xóa một file, phải xóa tên file và tất cả các hard link của nó (link count bằng 0)
* Hard link không tạo được với thư mục và không tạo được với file nằm trên một partition khác.
* Lệnh ln: ln <path/tên file> <tên hard link>.
Soft link (còn gọi là Symbolic link hoặc symlink) là một liên kết tạo một đường dẫn khác đến thư mục hoặc file gốc.

**Soft link** (còn gọi là Symbolic link hoặc symlink) là một liên kết tạo một đường dẫn khác đến thư mục hoặc file gốc.

* Ví dụ file gốc passwd có đường dẫn là /etc/passwd. Trong thư mục /home/user tạo một soft link đặt tên là “password” trỏ đến file đó. Như vậy đường dẫn mới đến file /etc/passwd là /home/user/password. Khi truy cập đến một trong hai đường dẫn trên đều là truy cập đến file passwd.

* Nếu đổi tên, xóa hoặc chuyển file gốc sang thư mục khác thì soft link không có giá trị. Khác với hard link, khi xóa file có soft link, file bị xóa thật.
* Có thể tạo soft link với thư mục và file nằm trên partition khác.
* Soft link tạo bằng lệnh ln -s <path/tên file> <tên soft link>


