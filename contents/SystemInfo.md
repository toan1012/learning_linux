

hostname [Op]

Example-1:

To print the hostname of the system :

$ hostname

output:

# hostname
testserver.tutorialspoint.com

Example-2:

To print IP address of the computer :

$ hostname -i

output:

192.168.134.128

Example-3:

To print the domain name :

$ hostname -d

output:

tutorialspoint.com

Example-4:

To print short hostname :

$ hostname -s

output:

testserver



CHECK INODE

df -i : số lượng inode
df -h : số lượng hệ thống đĩa con trống


Linux system info

Trong bài này ta sẽ tìm cách xem thông tin của hệ thống linux như RAM, CPU, hard disk, hostname, log.. 
Nhấn mạnh thêm, những hoạt động bất thường từ phần cứng và phần mềm đều lưu lại trên log của hệ điều hành, 
Vì vậy, việc biết đọc log là điều rất quan trọng.

Thêm một bài học từ Pro đi trước: học lệnh phải hiểu option. Hiểu về tên viết tắt, hiểu ý nghĩa. Nói là vậy, nhưng
cũng không cần cố học tất cả option. Mỗi lệnh hãy tự đặt cho mình giới hạn tối thiểu hiểu 3 option.


## Lệnh uname

Cho biết thông tin hệ thống. 

| Tùy chọn | Cách dùng              | Kết quả ví dụ                                |
|----------|------------------------|----------------------------------------------|
| -s       | tên kernel             | Linux                                        |
| -n       | nodename               | localhost.localdomain                        |
| -r       | tên kernel phát hành   | 3.13.0-32-generic                            |
| -v       | Tên phiên bản kernel   | #57-Ubuntu SMP Tue Jul  15 03:51:08 UTC 2014 |
| -m       | Kiểu kiến trúc máy     | x86_64                                       |
| -i       | Nền tảng phần cứng     | x86_64                                       |
| -o       | Hệ điều hành đang dùng | GNU/Linux                                    |
| -a       | Hiển thị tất cả        |                                              |



## Lệnh lsb_release

Để biết cụ thể hơn về phiên bản OS đang sử dụng ta dùng lệnh lsb_release

| Tùy chọn | Cách dùng           | Kết quả ví dụ                                                                                                            |
|----------|---------------------|--------------------------------------------------------------------------------------------------------------------------|
| -i       | id                  | Distributor ID: Ubuntu                                                                                                   |
| -d       | mô tả               | Description:,Ubuntu 14.04.1 LTS                                                                                          |
| -r       | ký hiệu bản release | Release:,14.04                                                                                                           |
| -c       | Tên của phiên bản   | Codename:,trusty                                                                                                         |
| -a       | Tất cả thông tin    | No LSB modules are available.  Distributor ID: Ubuntu  Description:,Ubuntu 14.04.1 LTS  Release:,14.04  Codename:,trusty |


## Lệnh hostname

Thông tin về hostname.

Xem thông tin hostname

`# cat /etc/hostname`

Thiết lập hostname mới

```
# hostname NEW_NAME
# reboot
```

## Lệnh df

Lệnh để chỉ ra thông tin về hệ thống đĩa.

Những lệnh hay dùng

```
toandm@localhost:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        67G  7.0G   58G  11% /
none            4.0K     0  4.0K   0% /sys/fs/cgroup
udev            2.0G  4.0K  2.0G   1% /dev
tmpfs           396M  424K  395M   1% /run
none            5.0M     0  5.0M   0% /run/lock
none            2.0G     0  2.0G   0% /run/shm
none            100M     0  100M   0% /run/user
```

Để xem inode ta dùng option -i (inode)

```
toandm@localhost:~$ df -i
Filesystem      Inodes  IUsed   IFree IUse% Mounted on
/dev/vda1      4456448 104164 4352284    3% /
none            506009      4  506005    1% /sys/fs/cgroup
udev            503197    422  502775    1% /dev
tmpfs           506009    388  505621    1% /run
none            506009      2  506007    1% /run/lock
none            506009      1  506008    1% /run/shm
none            506009      2  506007    1% /run/user
```

Để xem toàn bộ thông tin dùng tùy chọn -a (all) 

```
toandm@localhost:~$ df -a
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/vda1       70045596 7286408  59824968  11% /
proc                   0       0         0    - /proc
sysfs                  0       0         0    - /sys
none                   4       0         4   0% /sys/fs/cgroup
none                   0       0         0    - /sys/fs/fuse/connections
none                   0       0         0    - /sys/kernel/debug
none                   0       0         0    - /sys/kernel/security
udev             2012788       4   2012784   1% /dev
devpts                 0       0         0    - /dev/pts
tmpfs             404808     424    404384   1% /run
none                5120       0      5120   0% /run/lock
none             2024036       0   2024036   0% /run/shm
none              102400       0    102400   0% /run/user
none                   0       0         0    - /sys/fs/pstore
systemd                0       0         0    - /sys/fs/cgroup/systemd
```

## Thông tin trong thư mục /proc

/proc là thư mục chứa file chứa thông tin về hệ thống và hệ điều hành. Để xem được ta dùng lệnh cat

Ta có thể xem các thông tin trong  

```
/proc/cpuinfo
/proc/interrupts
/proc/meminfo
/proc/mounts
/proc/partitions
/proc/version
/proc/<process-id-#>
/proc/sys
```

Để xem thông tin:  

* Memory thì cat file /proc/meminfo.
* CPU thì cat file /proc/cpuinfo.
* Thông tin phân vùng /proc/partitions.
* Thông tin các interrupts hệ thống /proc/interrupts.












