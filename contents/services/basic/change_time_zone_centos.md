# Cap nhat mui gio tren centos (Linux)

Vấn đề thời gian ít được chú ý khi cài đặt server hay VMs. Nhưng việc ứng dụng chạy sai giờ có thể gây tình trạng giám sát sai, hoặc hơn nữa là không chạy.

– Kiểm tra múi giờ hiện tại:

`# date`

– Cập nhật lại timezone sang Asia/Ho_Chi_Minh:

```
# rm -f /etc/localtime
# ln -s /usr/share/zoneinfo/Asia/Ho_Chi_Minh /etc/localtime
```

Ngoài ra, có thể lựa chọn các timezone khác tại /usr/share/zoneinfo/ 

– Đồng bộ Network Time Protocol NTP về server Việt Nam:

Cài đặt NTP

`# yum install -y ntpdate`

Đồng bộ NTP server Việt Nam

`# ntpdate vn.pool.ntp.org`

Tuy vậy, hiện tại chỉ có 4 servers tại Việt Nam nên bạn có thể sử dụng server Asia

`# ntpdate asia.pool.ntp.org`

Search danh sách bằng cụm từ "NTP Pool Servers" Ngon nhất là server apple "time.apple.com"