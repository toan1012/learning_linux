# Lab 1: Tạo logical volume.

A [link](http://example.com "Title").


## Mục tiêu

* Mục tiêu của bài lab này là ta sẽ tạo được các logical volume để nhìn thấy đúng mô hình của LVM (tức là bao gồm 3 phần: 
physical volume, volume group, logical volume). 

* Hiểu được các câu lệnh tạo, lệnh kiểm tra của từng thành phần LVM bao gồm: pvcreate, pvs, pvdisplay, vgcreate, vgs, vgdisplay, lvcreate, lvs, lvdisplay.

* Ngoài ra, ta cũng nhớ lại những lệnh kiểm tra ổ đĩa, tạo phân vùng đĩa cứng bằng fdisk, định dạng phân vùng bằng lệnh mkfs, lệnh gán phân vùng (mount) ổ đĩa 
cho 1 thư mục trên FHS (Filesystem Hierarchy Standard) để sử dụng và còn 1 số lệnh khác sẽ được nói tới trong bài lab. 

## Điều kiện

Một máy ảo trên VMware chạy hệ điều hành ubuntu 16.04 có 3 ổ đĩa, có card mạng bridge hoặc NAT

## Tiến hành. 

### Bước 1: Cài đặt chương trình lvm2 trên máy ảo.

```
apt-get install lvm2 -y
```

### Bước 2: Thao tác tạo phân vùng từ ổ vật lý bằng `fdisk`

Trước khi tạo phân vùng cho ổ đĩa thì hiện trạng như sau:

```
# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0     8G  0 disk
├─sda1   8:1    0     7G  0 part /
├─sda2   8:2    0     1K  0 part
└─sda5   8:5    0  1022M  0 part [SWAP]
sdb      8:16   0    10G  0 disk
sdc      8:32   0    10G  0 disk
```

Ta đang có 2 ổ cứng vật lý sdb, sdc chưa dùng vào việc gì. Để sử dụng ta phải phân vùng cho ổ đĩa. Mỗi ổ sdb,c được chia thành 
2 phân cùng sd{b,c}{1,2}

```
# fdisk /dev/sdb

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-20971519, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519): 10971519

Command (m for help): n
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): p
Partition number (1-4, default 2):
Using default value 2
First sector (10971520-20971519, default 10971520):
Using default value 10971520
Last sector, +sectors or +size{K,M,G} (10971520-20971519, default 20971519):
Using default value 20971519

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Rất chú ý dòng command "w" để save những thay đổi đã làm. Kiểm tra lại để thấy sự khác biệt

```
# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0     8G  0 disk
├─sda1   8:1    0     7G  0 part /
├─sda2   8:2    0     1K  0 part
└─sda5   8:5    0  1022M  0 part [SWAP]
sdb      8:16   0    10G  0 disk
├─sdb1   8:17   0   5.2G  0 part
└─sdb2   8:18   0   4.8G  0 part
sdc      8:32   0    10G  0 disk
├─sdc1   8:33   0   5.2G  0 part
└─sdc2   8:34   0   4.8G  0 part
```

### Bước 3: Tạo physic volume (PV)

Kiểm tra các PV đã được tạo bằng lệnh `pvs` hoặc `pvdisplay`

Tạo PV bằng `pvcreate` 

```
# pvs
# pvcreate /dev/sdb1 /dev/sdc1
  Physical volume "/dev/sdb1" successfully created
  Physical volume "/dev/sdc1" successfully created
# pvs
  PV         VG   Fmt  Attr PSize PFree
  /dev/sdb1       lvm2 a--  5.23g 5.23g
  /dev/sdc1       lvm2 a--  5.23g 5.23
```

Để ý là trường VG của 2 PV đang trống chứng tỏ chúng chưa thuộc một Volume Group (VG) nào. bước tiếp theo ta
sẽ tạo VG và add PVs vào đó.

### Bước 4: Tạo VG 

Kiểm tra các VG đã tồn tại bằng lệnh `vgs` hoặc `vgdisplay`

Tạo VG mới bằng lênh `vgcreate`, truyền tham số đầu tiên là tên của VG (vg_lvm), tiếp theo là các PVs thêm vào group 

Làm xong kiểm tra lại để thấy thay đổi: trường VG của 2 PV đã được điền.

```
# vgs
  No volume groups found
# vgcreate vg_lvm /dev/sdb1 /dev/sdc1
  Volume group "vg_lvm" successfully created
# vgs
  VG     #PV #LV #SN Attr   VSize  VFree
  vg_lvm   2   0   0 wz--n- 10.45g 10.45g
  
# pvs
PV         VG     Fmt  Attr PSize PFree
/dev/sdb1  vg_lvm lvm2 a--  5.23g 5.23g
/dev/sdc1  vg_lvm lvm2 a--  5.23g 5.23g
```

Đến bước này ta có được 1 VG. Hãy hình dung VG ấy như 1 đĩa cứng, muốn ghi dữ liệu lên cần phân vùng và định dạng cho phân vùng ấy.
Bước tiếp theo ta sẽ làm như vậy. Tạo ra logical volume (LV) từ VG đã có.

### Bước 5: Tạo LV

Kiểm tra sự tồn tại của các LV bằng lệnh `lvs` hoặc `lvdisplay`

Tạo LV mới bằng lệnh `lvcreate` và thêm các thông số về tên phân vùng và dung lượng. Giống như với việc phân vùng ổ cứng thông thường

```
#lvs
# lvcreate -L 10G -n lv_test vg_lvm
  Logical volume "lv_test" created
# lvs
  LV      VG     Attr      LSize  Pool Origin Data%  Move Log Copy%  Convert
  lv_test vg_lvm -wi-a---- 10.00g
```

### Bước 6: Định dạng và mount.

Kiểu định dạng ở đây là ext4

```
# mkfs -t ext4 /dev/vg_lvm/lv_test
# mount /dev/vg_lvm/lv_test /home/anonymous
# df -h
# df -h
Filesystem                  Size  Used Avail Use% Mounted on
udev                        477M   12K  477M   1% /dev
tmpfs                        98M  1.0M   97M   2% /run
/dev/sda1                   6.8G  1.4G  5.1G  21% /
none                        4.0K     0  4.0K   0% /sys/fs/cgroup
none                        5.0M     0  5.0M   0% /run/lock
none                        488M     0  488M   0% /run/shm
none                        100M     0  100M   0% /run/user
/dev/mapper/vg_lvm-lv_test  9.8G   23M  9.2G   1% /home/anonymous
```

Nếu muốn disk sẽ tự động mount khi reboot lại OS, ta cần phải thực hiện cấu hình như sau:

```
vi /etc/fstab
```

sau đó thêm nội dung như sau vào file và lưu lại:

```
/dev/mapper/vg_lvm-lv_test /home/anonymous ext4 defaults 0 0
```

Hãy kiểm tra kết quả bằng việc chạy câu lệnh sau và restart lại OS:

mount -av

Lưu ý: Khi cài đặt nhiều distro linux mới, chương trình cài đặt sẽ hỏi có cài lvm không ( điển hình
có thể thấy trên centos 6) thì ta không được để /boot chạy lvm, hãy để nó chạy trên 1 phân vùng vật lý. 


**Tham khảo**

[https://www.tecmint.com/create-lvm-storage-in-linux/](https://www.tecmint.com/create-lvm-storage-in-linux/)

