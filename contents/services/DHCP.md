# Cài đặt DHCP server

Việc cài đặt DHCP server trên VMware cần chú ý. Trên card mạng sử dụng cho máy ảo (Host-only) 
cần bỏ tính năng cấp DHCP. tránh 2 đối tượng cấp đồng thời DHCP. Còn điều quan trọng, khai báo 
card mạng mới trong /etc/network/interface sau đó restart card mạng

Hệ điều hành sử dụng là ubuntu 14.04LTS. Với phiên bản 16.04LTS đang thử ngiệm nhưng
hiện giờ đang gặp lỗi như sau. 

```
toan@ubuntu:~$ systemctl restart isc-dhcp-server.service
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to restart 'isc-dhcp-server.service'.
Authenticating as: toan do,,, (toan)
Password:
==== AUTHENTICATION COMPLETE ===
toan@ubuntu:~$ systemctl status isc-dhcp-server.service
● isc-dhcp-server.service - ISC DHCP IPv4 server
   Loaded: loaded (/lib/systemd/system/isc-dhcp-server.service; enabled; vendor preset: enabled)
   Active: failed (Result: exit-code) since Wed 2018-03-21 08:24:30 ICT; 9s ago
     Docs: man:dhcpd(8)
  Process: 2772 ExecStart=/bin/sh -ec      CONFIG_FILE=/etc/dhcp/dhcpd.conf;      if [ -f /etc/ltsp/dhcpd.conf ]; then CONFIG_FILE=/etc/ltsp/dhcpd.conf; fi;      [ -e
 Main PID: 2772 (code=exited, status=1/FAILURE)

Mar 21 08:24:30 ubuntu dhcpd[2772]: If you think you have received this message due to a bug rather
Mar 21 08:24:30 ubuntu sh[2772]: If you think you have received this message due to a bug rather
Mar 21 08:24:30 ubuntu sh[2772]: than a configuration issue please read the section on submitting
Mar 21 08:24:30 ubuntu sh[2772]: bugs on either our web page at www.isc.org or in the README file
Mar 21 08:24:30 ubuntu sh[2772]: before submitting a bug.  These pages explain the proper
Mar 21 08:24:30 ubuntu sh[2772]: process and the information we find helpful for debugging..
Mar 21 08:24:30 ubuntu sh[2772]: exiting.
Mar 21 08:24:30 ubuntu systemd[1]: isc-dhcp-server.service: Main process exited, code=exited, status=1/FAILURE
Mar 21 08:24:30 ubuntu systemd[1]: isc-dhcp-server.service: Unit entered failed state.
Mar 21 08:24:30 ubuntu systemd[1]: isc-dhcp-server.service: Failed with result 'exit-code'.
lines 1-17/17 (END)
```

Lỗi trên lặp lại sau lần đặt 2 host fixed address trùng nhau => lỗi syntax. 



Tiếp tục việc cài đặt (nhớ dùng quyền root)

B1: apt-get update

B2: apt-get install isc-dhcp-server -y

B3: config file /etc/default/isc-dhcp-server bằng cách điền tên card mạng sử dụng 

```
INTERFACES="ens39"
```

B4: config file /etc/dhcp/dhcp.conf thêm đoạn khai báo subnet bên dưới. Chú ý bỏ comment ở 

```
subnet 192.168.75.0 netmask 255.255.255.0 {
  range 192.168.75.50 192.168.75.150;
  option domain-name-servers ns1.internal.example.org;
  option domain-name "internal.example.org";
  option routers 192.168.75.1;
  option broadcast-address 192.168.75.255;
  default-lease-time 600;
  max-lease-time 7200;
}
```

**Quy hoạch IP**

Với 1 mạng nhỏ (ví dụ có 254 host) thì cần phân hoạch IP thành 3 nhóm: 

* Nhóm static để đặt máy chủ.

* Nhóm manual trong dhcp dùng để fixed address cho 1 số đối tượng (ví dụ máy in, boss), thường ngoài range dhcp.

* Nhóm DHCP thực sự cấp trong range(pool).

**renew hoặc release IP in eth0**

Để renew một IP trên eth0 interface gõ lệnh:

```
$ sudo dhclient -r eth0
$ sudo dhclient eth0
```

Nếu dùng wireless interface:

```
sudo dhclient -v -r eth0
sudo dhclient -v eth0
```

DHCP server muốn sử dụng DNS chính là IP server thì cần cấu hình DNS server có cùng địa chỉ vs DHCP server. 
Phần cấu hình DNS server sẽ được thực hiện trong bài khác. 


Chú ý: DHCP server lắng nghe trên cổng 67 còn DHCP client lắng nghe trên cổng 68. Điều đó cũng đồng nghĩa các bản tin đi từ server
sẽ ra ở cổng 67 ngược lại bản tin đi từ client đi ra từ cổng 68. Nhìn vào đặc điểm này cũng sẽ suy ra được đâu là server, đâu là client.

* Khi bắt gói tin trên 2 đầu (CL,SV) kết quả giống nhau, nhưng khác nhau về thời gian.
Gói tin đi từ phía nào thì thời gian bắt được gói tin trên phía đó sớm hơn. 



[Tham khảo](https://www.tecmint.com/install-dhcp-server-in-ubuntu-debian/)
[Tham khảo](https://github.com/hocchudong/thuctap012017/blob/master/TamNT/DHCP/LAB%20DHCP%20server%20%20-%20DHCP%20Relay%20Agent.md)


