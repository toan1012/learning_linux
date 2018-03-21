# Tìm hiểu về swap 

## Khái niệm
Swap là một vùng trên ổ đĩa được sử dụng để lưu trữ các dữ liệu mà tạm thời không sử dụng trên bộ nhớ vật lý (RAM).
Swap được sử dụng khi hệ điều hành quyết định cần thêm bộ nhớ cho quá trình hoạt động mà bộ nhớ RAM không còn dư để 
sử dụng. Khi đó các tài nguyên và dữ liệu tạm thời không hoạt động trên bộ nhớ RAM sẽ được di chuyển để lưu trữ vào 
không gian Swap để giải phóng bộ nhớ RAM và sử dụng cho việc khác.

Có một vấn đề là thời gian truy cập vào vùng Swap là chậm hơn rất nhiều, do đó bạn không nên coi việc sử dụng Swap là một phương pháp thay thế 
hoàn hảo cho bộ nhớ vật lý (RAM). Swap có thể là một phân vùng dành riêng cho Swap, một tập tin Swap hoặc một sự kết hợp của 
phân vùng và tập tin Swap. Thông thường ta hay để là phân vùng.

## Lợi ích

Bản chất sử dụng Swap là lấy ổ cứng làm RAM, tuy nó là chậm nhưng nó vẫn tốt hơn là không sử dụng nếu máy tính không có đủ lượng RAM. 

Tối ưu hóa bộ nhớ – Hệ thống sẽ di chuyển các tài nguyên và dữ liệu hiện không được sử dụng trong bộ nhớ RAM đến Swap, điều này giúp hệ thống 
phục vụ cho các mục đích khác tốt hơn.

Tránh các trường hợp không lường trước – Trong một số trường hợp, bộ nhớ dành cho các chương trình không đủ, hoặc một chương trình nào 
đó ngốn RAM bất thường khi đó Swap sẽ được sử dụng 
để hệ thống có thể được tiếp tục chạy (mặc dù chậm hơn bình thường) thay vì hệ thống đột ngột dừng lại vì thiếu bộ nhớ.

## Kích thước của swap

Nếu RAM ít hơn hoặc bằng 1GB, nên sử dụng Swap có kích thước tối thiểu là bằng với lượng RAM. Cũng có ý kiến
 đề nghị rằng kích thước của Swap tối đa là gấp đôi dung lượng RAM, điều này tùy thuộc vào dung lượng ổ đĩa bạn đang có.
Có một nhược điểm khi bạn thiết lập kích thước của Swap quá lớn, đó là bạn đang lãng phí dung lượng ổ đĩa mặc dù Swap không được sử dụng.

## Kiểm tra dung lượng swap

Dùng lệnh `free -m` dòng thứ 2 thể hiện dung lượng swap. Free là phần còn lại. Used là đã dùng 

## Tạo file swap

```
sudo dd if=/dev/zero of=/swapfile bs=1M count=512
512+0 records in
512+0 records out
536870912 bytes (537 MB) copied, 2.3815 s, 225 MB/s
```

Thiết lập quyền cho file 

`sudo chmod 600 /swapfile`

Định dạng swap cho file 

`sudo mkswap /swapfile #Lệnh này dùng để chỉ ra vùng swap`

Kích hoạt file swap

`swapom /swapfile`

Kiểm tra dùng lệnh

`swapon -s #tương đương với cat /proc/swap` 

Kích hoạt lại swap cho mỗi lần khởi động tiếp theo hoặc thiết lập để hệ thống tự động kích hoạt và sử dụng swap.

`echo "/swapfile  none  swap  sw  0 0" | sudo tee -a /etc/fstab`

## Thiết lập swappiness

Tham số Swappiness sẽ quyết định khi nào các tài nguyên và dữ liệu được lưu giữ trong bộ nhớ RAM sẽ được di chuyển đến không gian Swap.
 Thời gian để truy cập đến Swap sẽ chậm hơn trên bộ nhớ RAM, nên chỉ định thời điểm hợp lý để hệ thống sử dụng Swap.
* Swappiness có giá trị từ 0 đến 100.
* Swappiness có giá trị càng lớn thì hệ thống sẽ sử dụng Swap càng sớm càng tốt.
* Swappiness có giá trị càng nhỏ thì hệ thống sẽ sử dụng Swap càng chậm càng tốt.
Ví dụ: bộ nhớ RAM củ là 4GB mà thiết lập giá trị của “Swappiness” là 50. Khi hệ thống sử dụng 50% bộ nhớ RAM (2GB) thì sẽ bắt đầu sử dụng Swap.
Nếu thời gian giải phóng bộ nhớ RAM chậm hơn thời gian mà các chương trình sẽ tiêu thụ dấn đến quá tải cho hệ thống, hãy thiết lập để 
sử dụng Swap càng sớm càng tốt. Trì hoãn sử dụng Swap nếu bộ nhớ RAM giải phóng kịp thời để phục vụ cho các chương trình. 
Bởi vì sử dụng bộ nhớ RAM sẽ giúp hệ thống chạy nhanh hơn.

Để kiểm tra giá trị của Swappiness, sử dụng lệnh sau:

`cat /proc/sys/vm/swappiness`

Kết quả là 1 số. 

Để thay đổi giá trị của Swappiness, sử dụng lệnh như dưới đây:

`sudo sysctl vm.swappiness=10 #giá trị 10 thay đổi tùy nhu cầu`

Để thiết lập giá trị vĩnh viễn cho Swappiness thêm 

```
sudo vi /etc/sysctl.conf
vm.swappiness=10
```


## Tham khảo 

[https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-16-04)



