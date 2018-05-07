# Gửi thông báo bằng email trong zabbix

## 1. Tạo một email để nhận thư

Lưu ý: Không cài bảo mật 2 lớp, nếu có cài, thì tắt đi.

## 2. Download và config lại file script gửi mail

Đứng trên thư mục /usr/lib/zabbix/alertscripts//usr/lib/zabbix/alertscripts gõ lệnh

```
wget https://gist.github.com/superdaigo/3754055/raw/0cf9c4fa5c2be466c121dc29d574dcf4122d1b17/zabbix-alert-smtp.sh
chmod +x zabbix-alert-smtp.sh
```

Vào sửa 2 trường trong file zabbix-alert-smtp.sh vừa tải về cho phù hợp với account gmail. 

```
MAIL_ACCOUNT = 'account@gmail.com'
MAIL_PASSWORD = '*****'
```

Kiểm tra tường lửa có mở port 25, 587 không? Nếu không thì mở ra.

```
firewall-cmd –permanent –add-port=25/tcp
firewall-cmd –permanent –add-port=587/tcp
firewall-cmd –reload
```

Chạy kiểm tra script viết đúng không.

`./zabbix-alert-smtp.sh account@gmail.com "Test email" "Hello Zabbix"`

## 3. Khởi tạo media type trên web

Vào Admininstration|Media Types|Create media type . Sau đó điền đúng các thông tin vào

![Imgur](https://i.imgur.com/WbSsisL.png)

## 4. Gán cho user với hành động gửi mail

Vào Admininstration|Users. Chọn user sẽ gửi mail (ví dụ Admin). Trong mục Media ta sẽ click vào add để điền thông tin 

Một hộp thoại mở ra, ta sẽ điền media type, send to (gửi tới email nào).

![Imgur](https://i.imgur.com/DADimGF.png)

## 5. Cài đặt một action

Khi có 1 sự kiện xảy ra, một trigger bật lên, tiếp theo phải có hành động sau khi phát hiện được vấn đề. Hành động ở đây là gửi mail
được thực hiện bởi kiểu media ta quy định ở phái trên.

Vào Configuration|Actions|Create Actions to Zabbix administrators. Tạo một sự kiện 

Chọn tab Operation -> Operation thêm mới 1 hoạt động trong đó cần điền thông tin hoạt động sẽ gửi cho ai, gửi bằng cách nào (email, hay sms, hay media 
type mới tạo). Trong trường hợp của mình, tôi chọn tới tên media type đã tạo ở trên. Tức là, ta sẽ gửi mail bằng script.

![Imgur](https://i.imgur.com/6Il8eFM.png)


## 6. Làm thế nào để gửi email leo thang

Thực tế là mỗi 1 loại cảnh báo sẽ cần gửi tới 1 người hoặc 1 nhóm người. Vì vậy ta cần cấu hình để có thể gửi mail bằng cách đó.

Tôi đặt ra 1 tình huống đó là khoảng trống của /boot nhở hơn 40% thì chia trigger thuộc loại infomation, hành động tiếp theo là gửi mail cho 
email A@gmail.com . Khi /boot nhở hơn 10% thì trigger thuộc loại High, gửi mail cho A@gmail.com và B@gmail.com.

Vậy thì về nguyên tắc ta tạo ra 2 action cho 2 trigger của cùng 1 user. Tức là, trong bước 4, ta sẽ add 2 email. 1 email chỉ gửi khi trigger là infomation
còn 1 sẽ gửi khi trigger là high.


## Lỗi đã gặp

`Cannot exectue command "/usr/lib/zabbix/alertscripts//usr/lib/zabbix/alertscripts/zabbix-alert-smtp.sh": [2] No such file or directory`

Nguyên nhân do khi tạo media type copy đường dẫn tuyệt đối của script vào, mà đúng ra chỉ cần tên zabbix-alert-smtp.sh

