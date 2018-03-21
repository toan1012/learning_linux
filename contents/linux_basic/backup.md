
# Backup the data

Để backup data ta có thể dùng nhiều lệnh, đơn giản nhất là lệnh `cp` theo kiểu

`cp source dest`

Kinh nghiệm trong khi backup là phải điền đầy đủ thông tin về tên_filebk và thời gian.
và phải xem quyền và sở hữu của file thuộc về user nào. Trong rất nhiều trường hợp, khi ta
update code có thể code mới không chạy do ta không để ý tới vấn đề này. Trong trường hợp này 
sử dụng kiến thức bài về 'permission file'

Lấy ví dụ:

`cp -a code.sh code.sh.bak201803050152`

hoặc nếu muốn copy từ máy này tới máy kia ta có thể dùng `scp`

`scp path_to_parent file[dir] user@ip_remote:path/to/save/file` 

Để tăng hiệu năng ta có thể dùng lệnh `rsync`.Lệnh này có khả năng đồng bộ toàn bộ cây thư mục chọn backup.
Cơ chế của rsync là kiểm tra nếu file copy có tồn tại không. Nếu tồn tại thì kiểm tra có thay đổi kích thước hay thay đổi
mtime không. rsync sẽ tránh việc copy các file không cần thiết để tiết kiệm thời gian, tăng tốc độ. 

Khả năng copy theo kiểu đệ quy với option -r cũng là cách để nó đồng bộ cây thư mục đích với gốc.

![rsync](https://i.imgur.com/9qgHGno.png)

# Nén dữ liệu

Để giảm dung lượng file phục vụ cho quá trình lưu trữ, truyền file ta dùng các phần mềm để nén file, thư mục lại

**gzip**

Là một tool nén file lâu đời, có khả năng tương thích với nhiều distro linux được viết trên thuật toán "DEFLATE".

gzip có điểm mạnh ở tốc độ nén và giải nén file nhanh, tương thích nhiều. Nhưng ngược lại, điểm yếu là khả năng nén không cao.

Đuôi file mở rộng là `.gz`. Lệnh thực hiện `gzip soucefile`

Thêm option -r (recurrent) để nén cả file và thư mục đệ quy.

Thêm option -1 đến -9 để đặt tốc độ và chất lượng nén. -1 nhanh nhất nhưng nén ít nhất. `gzip -9 sourefile`

Để giải nén ta dùng lệnh `gzip -d file_compressed`

**bzip2** 

Được viết dựa trên thuật toán "Burrows-Wheeler".

Có nhiều điểm tốt hơn gzip về khả năng nén 

Đuôi mở rộng là `.bz2` 

Để giảm việc dùng tài nguyên ram bzip2 dùng option -s (small) đánh đổi lại là chất lương nén không tốt `bzip2 -s file`

Cũng giống như gzip, gzip2 cũng có 9 tốc độ nén tỷ lệ nghich với chất lượng. Ví dụ 	`gzip -1 soucefile`

Để giải nén ta dùng option -d như sau: `bzip2 -d file.bz2`

**xz**
 
Là tool mới ra đời có tốc độ và dung lượng file sau nén tốt nhất so với 2 loại giới thiệu trên. Nó dùng thuật toán LZMA2. 

Cách xử dụng cũng tương tự 2 lệnh trên. 

**Lệnh tar**

Dùng để giải nén file hay thư mục. 

| Ví dụ   :                     | Cách dùng                            |
|------------------------------|--------------------------------------|
| tar xvf mydir.tar            | Giải nén file mydir.tả trong thư mục |
| tar zcvf mydir.tar.gz mydir  | Tạo lưu trữ và giải nén file gzip    |
| tar jcvf mydir.tar.bz2 mydir | Tạo lưu trữ và giải nén file gzip2   |
| tar Jcvf mydir.tar.bz2 mydir | Tạo lưu trữ và giải nén file xz      |                                       

**Tham khảo**

[https://www.digitalocean.com/community/tutorials/an-introduction-to-file-compression-tools-on-linux-servers](https://www.digitalocean.com/community/tutorials/an-introduction-to-file-compression-tools-on-linux-servers
)






