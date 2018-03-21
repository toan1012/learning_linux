# TCPDUMP

Tcpdump là công cụ được phát triển nhằm mục đích phân tích các gói dữ liệu mạng theo dòng lệnh.

Dưới đây là 1 phần cách dùng của tcpdump. Những option có thể kết hợp với nhau tạo ra rất nhiều cách dùng khác.

1.Capture packets từ một ethernet interface sử dụng tcpdump -i

`tcpdump –i ens39`

2. Capture N packets sử dụng `tcpdump –c`

Thực thi câu tcpdump command , nó sẽ capture packets cho đến khi bản hủy bỏ tcpdump coomand . Sử dụng -c option bạn có thể chỉ định number of packets để capture .

`tcpdum –c 2 –i ens39`

3. Hiển thị gói tin bắt được trên interface bằng mã ASCII dùng `tcpdump –A`

`tcpdump -A -c 2 -i ens39`

4.Hiển thị gói tin bắt được trên interface bằng mã ASCII và HEX `tcpdump -XX`

`tcpdump -XX -c 2 -i ens39`

5. tcpdump cho phép bạn save packets vào file , và sau dó bạn có thể sử dụng packet file để analysis .

`tcpdump –w test.pcap –i ens39`

* đuôi file nên để .pcap và file này có thể đọc bởi bất kỳ network protocol analyzer nào .

* Mở file test.pcap với Wireshark .

6. Đọc file đã lưu `tcpdump –r`

`tcpdump –r test.pcap`

7. Capture packets with IP address using tcpdump -n

`tcpdump –n –i ens39`

Trong ví dụ trên , ta thấy in ra packets với địa chỉ DNS address , nhưng không in ra IP address .

Để capture packets và display IP address , ta sử dụng -n option .

8. Đọc packets lớn hơn N bytes

Chỉ nhân các packets có độ dài lớn hơn N bytes sử dụng filter “greater”.

`tcpdump -w test.pcap greater 100`

9.Chỉ nhận packets của protocol nào đó

tcpdump có thể bắt gói tin từ nhiều protocols như wlan,ip,ip6,arp,rarp,decnet,tcp và udp.

`tcpdump –i ens39 arp`

10. Ghi packets nhỏ hơn N bytes

Chỉ nhận receive packets nhỏ hơn N bytes sử dụng fliter "less".

`tcpdump –w test.pcap less 1024`

11. Nhận packets trên 1 port cụ thể.

`tcpdump –i ens39 port 22`

12. Capture packets có destination IP và Port cụ thể.

Packets sẽ có source và destination IP và port numbers .

`tcpdump -i ens39 dst 172.16.69.176 and port 22`

13.Capture packets for particular source IP

`tcpdump -i ens39 src 172.16.69.2`

14. Sử dụng điều kiện "and" , "or" và "not" để fliter packets .

`tcpdump -i ens39 not arp and not rarp`

