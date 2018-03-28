# Thực hành ARP

Bài lab về ARP được thực hiện trên 3 công cụ: tcpdump trên linux, cisco packet tracer, wireshark

Mỗi công cụ có điểm mạnh riêng, sử dụng cho những mục đích khác nhau ví như tcpdump dùng bắt gói tin trên

linux, wireshark dùng bắt gói tin trên window(tất nhiên vẫn dùng được trên linux, nhưng không thích hợp cho môi

trường server), cisco packet tracer dùng giả lập và kiểm chứng nhanh lý thuyết nhờ khả năng capture từng giai đoạn

của gói tin trên đường truyền. 

## Mô hình lab ARP trên linux

![Imgur](https://i.imgur.com/DySvSLt.png)

**Mục tiêu**

* Bắt được gói tin arp bằng tcpdump 

* Xác định các message ARP

* Thực hiện thao tác thêm, xóa, xóa tất cả trên ARP cache

**Thực hiện**

Trên mỗi VM có 3 card mạng có 3 IP khác nhau. 

B1: show arp table bằng lệnh `arp -a`

```
root@dhcpserver:~# arp -a
? (192.168.75.1) at <incomplete> on ens39
? (192.168.11.254) at 00:50:56:f5:7a:b3 [ether] on ens38
? (192.168.75.52) at 00:0c:29:dd:2a:cb [ether] on ens39
? (192.168.75.50) at 00:50:56:c0:00:01 [ether] on ens39
? (192.168.11.1) at 00:50:56:c0:00:08 [ether] on ens38
? (192.168.11.130) at 00:0c:29:dd:2a:c1 [ether] on ens38
? (192.168.99.1) at 64:66:b3:d5:c5:0e [ether] on ens33
root@dhcpserver:~#
```

B2: Xoá hết các entries trong bảng arp cache trên bằng lệnh `ip -s -s neigh flush all`.
Nhưng ngay sau đó cần phải restart lại các card mạng bằng lệnh `ifdown -a&&ifup -a` .
Quá trình này cần tới quyền root và có thể gặp lỗi card mạng nào đó không bring up,
 lúc đó ta reboot lại máy.

```
toan@dhcpserver:~$ arp -a
? (192.168.75.1) at <incomplete> on ens39
? (192.168.11.254) at <incomplete> on ens38
? (192.168.75.52) at <incomplete> on ens39
? (192.168.75.50) at 00:50:56:c0:00:01 [ether] on ens39
? (192.168.11.1) at 00:50:56:c0:00:08 [ether] on ens38
? (192.168.11.130) at <incomplete> on ens38
? (192.168.99.1) at <incomplete> on ens33
```

Trong thực tế khi bật lên VM đã tìm các default gateway nên nó cập nhật ngay vào arp cache.
Ta sẽ thấy một kết quả tương tự như dưới

```
root@dhcpserver:~# arp -a
? (192.168.99.1) at 64:66:b3:d5:c5:0e [ether] on ens33
? (192.168.75.1) at <incomplete> on ens39
? (192.168.11.1) at 00:50:56:c0:00:08 [ether] on ens38
? (192.168.75.50) at 00:50:56:c0:00:01 [ether] on ens39
root@dhcpserver:~#
```

Để ý thấy `? (192.168.75.1) at <incomplete> on ens39` , do một nguyên nhân nào đó 
ta chưa xóa được entry đó, ta sẽ dùng lệnh xóa 1 entry 

B4: Xóa 1 entry bằng lệnh `arp -d 192.168.75.1` và sau đó ta cũng reboot card mạng 

B5: Sau khi làm trên cả 2 ta sẽ duplicate 1 phiên ssh (ví dụ trên máy có IP 192.168.11.130)
gõ sẵn lệnh ping 192.168.11.129. Mục đích: do 2 máy không biết nhau, trong cache không còn lưu
MAC của nhau nên khi dùng ping việc đầu tiên là arp sẽ thực hiện trước, đây là cách gián tiếp
để chạy arp. 

B6: Trên 2 tab ssh còn lại ta bật sẵn lệnh `tcpdump -i ens38 -XX -vv -e arp` để bắt tiến trình arp

Tùy chọn 

* -i là interface sẽ bắt. nếu ta ping giải mạng Hostonly ta dùng ens39, Bridge dùng en33

* -XX hiển thị mã ascii và hex của gói tin

* -vv hiện đầy đủ thông tin của gói tin (broadcast, request, reply)

* -e hiện MAC 

Kết quả 

```
root@dhcpserver:~# tcpdump -i ens38 -XX -vv -e arp
tcpdump: listening on ens38, link-type EN10MB (Ethernet), capture size 262144 bytes
10:21:44.362907 00:50:56:c0:00:08 (oui Unknown) > 00:0c:29:00:2d:00 (oui Unknown), ethertype ARP (0x0806), length 60: Ethernet (len 6), IPv4 (len 4), Request who-has 192.168.11.129 (00:0c:29:00:2d:00 (oui Unknown)) tell 192.168.11.1, length 46
        0x0000:  000c 2900 2d00 0050 56c0 0008 0806 0001  ..).-..PV.......
        0x0010:  0800 0604 0001 0050 56c0 0008 c0a8 0b01  .......PV.......
        0x0020:  000c 2900 2d00 c0a8 0b81 0000 0000 0000  ..).-...........
        0x0030:  0000 0000 0000 0000 0000 0000            ............
10:21:44.362967 00:0c:29:00:2d:00 (oui Unknown) > 00:50:56:c0:00:08 (oui Unknown), ethertype ARP (0x0806), length 42: Ethernet (len 6), IPv4 (len 4), Reply 192.168.11.129 is-at 00:0c:29:00:2d:00 (oui Unknown), length 28
        0x0000:  0050 56c0 0008 000c 2900 2d00 0806 0001  .PV.....).-.....
        0x0010:  0800 0604 0002 000c 2900 2d00 c0a8 0b81  ........).-.....
        0x0020:  0050 56c0 0008 c0a8 0b01                 .PV.......
```

SHA và SPA ở 2 gói tin trên đã đổi ngược.

## Mô hình trên cisco packet tracer


**Mục tiêu 1**

Nhìn được đường đi của gói tin, sự thay đổi của các trường mà quan trong nhất là SHA, SPA, THA, TPA của gói tin ARP trong mạng LAN

![Imgur](https://i.imgur.com/6QPSNXE.png)

Mô hình gồm 4 PC kết nối với 4 cổng của switch (trong cùng vlan mặc định) 

B1: Đặt IP tĩnh cho 4 PC. 192.168.0.2-5 (click đúp vào PC-> IP configuration-> đặt IP,gateway,dns)

B2: Chuyển từ chế độ realtime sang Simulation

![Imgur](https://i.imgur.com/4MaySbK.png)

B3: Trên PC0 : Click đúp PC -> Desktop -> command prompt->gõ ping tới IP của 1 máy khác trong mạng-> click Capture/Forward trong thanh màu vàng bên dưới. Mỗi 1 lần click gói tin sẽ
chuyển tiếp tới 1 thiết bị tiếp theo trên đường truyền.

B4: Đọc thông tin SHA, SPA, THA, TPA bằng cách click đúp vào hình message màu xanh. Dưới đây là những hình ảnh lab

Bắt đầu từ PC0

![Imgur](https://i.imgur.com/U8kcbFP.png)

Gói tin tới switch. tới đây gói tin sẽ forward tới tất cả các cổng trừ cổng src. SHA, SPA, THA, TPA không đổi. Để ý layer1 sẽ thay đổi về port

![Imgur](https://i.imgur.com/FLTstB3.png)

B5: Tại các máy, đọc IP đích (TPA) không đúng với chính nó, gói tin sẽ bị hủy. Máy nhận đúng sẽ tạo message reply với SHA, SPA là thông số của chính máy đó

còn THA, TPA là SHA, TPA của gói nguồn.

![Imgur](https://i.imgur.com/FLTstB3.png)

B6: gói tin được gửi lại. qua switch mà không có thông số gì thay đổi trong bước 5

![Imgur](https://i.imgur.com/dEed6n7.png)

B7: Gói tin từ switch sẽ gửi tới chính xác địa chỉ của PC0 theo kiểu unicast 

![Imgur](https://i.imgur.com/Df5ySEA.png)

Sau bước này, PC0 đã biết MAC của PC4, lúc này giao thức ICPM (ping) sẽ hoạt động. 


**Mục tiêu 2** 

Quan sát sự thay đổi của gói tin arp trong môi trường liên mạng (có router)

Đặc trưng của môi trường liên mạng là có router. Bản thân router sẽ drop gói tin quảng bá. nên nếu 2 máy tính 

ở 2 interface khác nhau của router sẽ không giao tiếp trực tiếp với nhau (tức không thể dùng chỉ MAC). Thế nên 

1 cách mà ARP đã thực hiện là thay vì hỏi "Ê máy tính A mày có MAC thế nào" nó sẽ làm "Ê router MAC port của mày là gì? mày gửi 
hộ tao cho thằng có IP là B nhé" router nhận được sẽ forward sang port của interface chứa thằng B và lại hỏi "Ê B, MAC của mày là gì"
đó là quá trình mà router đóng vai trò trung gian hỏi, phản hồi. Ta sẽ tìm hiểu sự thay đổi của 3 trường SHA, SPA, THA, TPA trên đường đi của message
từ PC0 tới PC3 (theo mô hình). 

**Mô hình **

![Imgur](https://i.imgur.com/FzAkcno.png)

Các máy đã được đặt IP tĩnh.

**Thực hiện**

B1: Kiểm tra xem arp cache PC0 đã có PC3 chưa bằng cách vào command prompt gõ lệnh `arp -a`. Nếu 
Kết quả "No ARP entries found"

B2: Vào chế độ simulation và làm tương tự như phần LAN. gõ lệnh ping 

Thực chất quá trình giống như trong mạng LAN chỉ có điều thay vì tìm địa chỉ MAC của PC3 thì PC0 sẽ tìm MAC của port gateway của network chứa PC0. 
phần còn lại là do router định tuyến. Về phía mình PC3 cũng chỉ biết MAC của port gateway ở phía network của nó. 








`









