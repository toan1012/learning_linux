# Thay đổi set FQDN

Để xem hostname dùng lệnh `hostname`

Để xem domain name dùng lệnh `hostname -d`

Để setup FQDN (Fully Qualified Domain Name)

* B1: Mở file /etc/hostname dưới quyền root để thay đổi hostname.

* B2: Mở file /etc/hosts thay đổi Domain name and FQDN.

```
127.0.0.1 localhost
127.0.1.1 hostname.abc.xyz hostname
```

B3: start service /etc/init.d/hostname.sh start rồi reboot
