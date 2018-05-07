# Gửi thông báo bằng email trong zabbix

## 1. Mục tiêu LAB

* Hiểu cách cảnh báo trên zabbix.

* Cấu hình các kiểu gửi mail trong zabbix: gửi 1 người, gửi nhiều người.

## 2. Mô hình LAB

![Imgur](https://i.imgur.com/7jd6k67.png)

## 3. IP Plainning

![Imgur](https://i.imgur.com/0oPHIDH.png)

## 4. Chuẩn bị

* OS cài CentOS7

* ứng dụng cài sẵn zabbix server, zabbix-client.

* 2 NICs: 

	* ens33: cài host-only
	
	* ens37: cài bridge

* 2 email bật xác minh nhiều lớp.

## 5. Cài đặt

Trong bài lab này, để đơn giản nhất có thể, ta cài zabbix server và zabbix agent trên cùng 1 server. Trong trường hợp server và agent riêng 
biệt thì chú ý, các lệnh trên bài lab này nằm trên zabbix-server.

### 5.1. Download và config lại file script gửi mail

Đứng trên thư mục /usr/lib/zabbix/alertscripts//usr/lib/zabbix/alertscripts gõ lệnh.

```
wget https://gist.github.com/superdaigo/3754055/raw/0cf9c4fa5c2be466c121dc29d574dcf4122d1b17/zabbix-alert-smtp.sh
chmod +x zabbix-alert-smtp.sh
```

Vào sửa 2 trường trong file zabbix-alert-smtp.sh vừa tải về cho phù hợp với account gmail. 

```
MAIL_ACCOUNT = 'A@gmail.com'
MAIL_PASSWORD = '*****'
```

Kiểm tra tường lửa có mở port 25, 587 không? Nếu không thì mở ra.

```
firewall-cmd –permanent –add-port=25/tcp
firewall-cmd –permanent –add-port=587/tcp
firewall-cmd –reload
```

Chạy kiểm tra script bằng lệnh:

`./zabbix-alert-smtp.sh A@gmail.com "Test email" "Hello Zabbix"`

### 5.2. Khởi tạo media type trên web

Vào Admininstration|Media Types|Create media type . Sau đó điền đúng các thông tin vào

![Imgur](https://i.imgur.com/BptfPEX.png)

Sau đó, 1 hộp thoại như dưới mở ra, ta điền các thông tin theo hướng dẫn bên dưới.

![Imgur](https://i.imgur.com/DtgeSAa.png)

Trong đó: 

1. Tên của media type 

2. Type: Loại media type. Chọn script vì ta dùng đoạn script vừa download ở trên để gửi mail

3. Script name: Tên của script. Default dir là /usr/lib/zabbix/alertscripts nên chỉ cần điền tên file script đặt trong thư mục kia.

4. Là trường sendto trong Configuration|Users|Media

![Imgur](https://i.imgur.com/kNTdy6Z.png)

5. subject là "Problem: {TRIGGER.NAME}"

6. message có cấu trúc như trong Configuration|Actions|Operation

```
Problem started at {EVENT.TIME} on {EVENT.DATE}
Problem name: {TRIGGER.NAME}
Host: {HOST.NAME}
Severity: {TRIGGER.SEVERITY}

Original problem ID: {EVENT.ID}
{TRIGGER.URL}
```

### 5.3. Gán cho user với hành động gửi mail

Zabbix có thể tạo ra nhiều user với quyền khác nhau để quản trị. Mỗi user có 1 cách thức để truyền thông cho người quản trị.
Trong bước này, ta sẽ setup cách mà user đó gửi thông báo. 

Vào Admininstration|Users. Chọn user sẽ gửi mail (ví dụ Admin). Trong mục Media ta sẽ click vào add để điền thông tin 

Một hộp thoại mở ra, ta sẽ điền media type, send to (gửi tới email nào).

![Imgur](https://i.imgur.com/sy3w4w3.png)

Trong đó:

* alias: là tên của user sẽ login vào zabbix

* Name và Surname không quan trọng để đặt tên cho dễ nhớ

* Groups: gán alias vào 1 nhóm

Sau đó ta chuyển sang tab Media. Làm theo hướng dẫn trong hình dưới.

![Imgur](https://i.imgur.com/4TUpmNg.png)

Trong đó:

* Type: Kiểu mà ta gửi (email, sms, hoặc 1 kiểu ta đã tạo ra)

* Send to: điền vào địa chỉ sẽ nhận mail.

* When active: Khi nào thì gửi. zabbix cho thiết lập theo thứ trong tuần, giờ, phút. Để mặc định thì bất kỳ khi nào có sự kiện
zabbix sẽ gửi.

* Use if severity: Chọn loại cảnh báo nào thì gửi.

### 5.4. Cài đặt một action

Khi có 1 sự kiện xảy ra, một trigger bật lên, tiếp theo phải có hành động sau khi phát hiện được vấn đề. Hành động ở đây là gửi mail
được thực hiện bởi kiểu media ta quy định ở phái trên.

Vào Configuration|Actions|Create Actions to Zabbix administrators. Tạo một sự kiện 

![Imgur](https://i.imgur.com/rjquK0x.png)

Chọn tab Operation -> Operation thêm mới 1 hoạt động trong đó cần điền thông tin hoạt động sẽ gửi cho ai, gửi bằng cách nào (email, hay sms, hay media 
type mới tạo). Trong trường hợp của mình, tôi chọn tới tên media type đã tạo ở trên. Tức là, ta sẽ gửi mail bằng script.

![Imgur](https://i.imgur.com/oKzbSSU.png)

Trong đó 

* Name là tên của action

* New condition: là loại điều kiên; ta đang dựa theo trigger thì để trigger.

* Chọn 1 trigger cần theo dõi

### 5.5. Làm thế nào để gửi email leo thang

Thực tế là mỗi 1 loại cảnh báo sẽ cần gửi tới 1 người hoặc 1 nhóm người. Vì vậy ta cần cấu hình để có thể gửi mail bằng cách đó.

Tôi đặt ra 1 tình huống đó là khoảng trống của /boot nhở hơn 40% thì chia trigger thuộc loại infomation, hành động tiếp theo là gửi mail cho 
email A@gmail.com . Khi /boot nhở hơn 10% thì trigger thuộc loại High, gửi mail cho A@gmail.com và B@gmail.com.

Vậy thì về nguyên tắc ta tạo ra 2 action cho 2 trigger của cùng 1 user. Tức là, trong bước 4, ta sẽ add 2 email. 1 email chỉ gửi khi trigger là infomation
còn 1 sẽ gửi khi trigger là high.


## 6. Lỗi đã gặp

`Cannot exectue command "/usr/lib/zabbix/alertscripts//usr/lib/zabbix/alertscripts/zabbix-alert-smtp.sh": [2] No such file or directory`

Nguyên nhân do khi tạo media type copy đường dẫn tuyệt đối của script vào, mà đúng ra chỉ cần tên zabbix-alert-smtp.sh

## 7. Link tham khảo

[link1](https://www.zabbix.com/documentation/3.4/manual/quickstart/notification)
