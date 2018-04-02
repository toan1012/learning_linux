# LVM

## 1.LVM là gì

Là phương pháp cho phép ấn định không gian đĩa cứng thành những logical Volume khiến cho việc thay đổi kích thước trở nên dễ dàng hơn (so với partition).

Với kỹ thuật Logical Volume Manager (LVM) có thể thay đổi kích thước mà không cần phải sửa lại table của OS, cũng như không làm hệ thống bị gián đoạn.

## 2. Các thành phần của LVM

![Imgur](https://i.imgur.com/zJBQqRx.png)

Hard drives - Drives: Thiết bị lưu trữ dữ liệu, ví dụ như là các loại ổ cứng HDD, SSD, USB...

Partition: Partitions là các phân vùng của Hard drives, mỗi Hard drives thường có 4 partition, trong đó partition bao gồm 2 loại là primary partition và extended partition

Primary partition là phân vùng chính, mỗi đĩa có tối đa 4 phân vùng primary.

Extended partition: Phân vùng mở rộng, có thể tạo những vùng logic

Physical Volumes:

* Là những đại vật lý hoặc phân vùng đĩa của bạn chẳng hạn như /dev/hda hoặc /dev/sdb1. Khi muốn sử dụng chỉ cần mount vào là được

* Là một cách gọi khác của partition trong kỹ thuật LVM, nó là những thành phần cơ bản được sử dụng bởi LVM. Một Physical Volume không thể mở rộng ra ngoài phạm vi một ổ đĩa.

Chúng ta có thể kết hợp nhiều Physical Volume lại thành Volume Groups để dễ dàng trong việc quản lý và phân chia.

Volume Group: Là một nhóm bao gồm nhiều Physical Volume trên những ổ đĩa khác nhau được kết hợp lại thành một Volume Group

* Volume Group được sử dụng để tạo ra các Logical Volume, trong đó người dùng có thể tạo, thay đổi kích thước, lưu trữ, gỡ bỏ và sử dụng.

* **boot loader không thể đọc /boot khi nó nằm trên Logical Volume Group**. Do đó không thể sử dụng kỹ thuật LVM với /boot mount point.

Logical Volume: Volume Group được chia nhỏ thành nhiều Logical Volume, mỗi Logical Volume có ý nghĩa tương tự như partition. Nó được dùng cho các mount point và được format 
với những định dạng khác nhau như ext2, ext3, ext4,…

## 3. Lab 

	## 3.1. Chuẩn bị
	
Tạo 1 máy ảo ubuntu 14 với 3 ổ cứng như hình

![Imgur](https://i.imgur.com/BpNhnTz.png)

Ổ đầu tiên cài hệ điều hành còn 3 ổ khác lab lvm

Cài lvm bằng lệnh `sudo apt-get install lvm2 -y`

	## 3.2. Các bước tạo Logical volume sử dụng LVM
	
**Bước 1: Kiểm tra các Hard Drives có trên hệ thống**

Lệnh `lsblk` 

Hiện ra cây ổ cứng hệ thống. Trong đó ổ sda sẽ có các phân vùng sda1,sda2,sda3 trong khi ổ
sdb, sdc chưa có phân vùng nào. 

**Bước 2: Tạo phân vùng cho các ổ sdb, sdc**

Để tạo phân vùng có thể dùng parted hoặc fdisk,.. ở đây tôi chọn fdisk

```
anonymous@toan:~$ sudo fdisk /dev/sdb
[sudo] password for anonymous:

Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)

Command (m for help): n
Partition type:
   p   primary (2 primary, 0 extended, 2 free)
   e   extended
Select (default p): p
Partition number (1-4, default 3): 1
Partition 1 is already defined.  Delete it before re-adding it.
```

Ở đây do t đã tạo phân vùng sdb1, sdb2 rồi nên chương trình báo như vậy. Tạo mới thì làm như hình, sau bước trên sẽ hỏi cylinder bắt đầu
và kết thúc, ta chọn theo dung lượng phân vùng cần. Nhớ là chọn w để save nếu không sẽ không tạo được

Làm xong kiểm tra lại thấy sdb đã có 2 phân vùng là sdb1 và sdb2 là đúng. Làm tương tự với sdc.

**Bước 3: Tạo physic volume**

Mục đích của bước này là báo cho pvcreate biết các phân vùng nào sẽ sử dụng để tạo LVM.

```
pvcreate /dev/sdb1
pvcreate /dev/sdb2
pvcreate /dev/sdc1
pvcreate /dev/sdc2
```

Nếu thành công gõ lệnh pvs sẽ ra kết quả tương tự như sau:

```
root@toan:~# pvs
  PV         VG      Fmt  Attr PSize PFree
  /dev/sdb1  vg-demo lvm2 a--  5.88g    0
  /dev/sdb2  vg-demo lvm2 a--  4.11g 3.88g
  /dev/sdc1  vg-demo lvm2 a--  5.88g    0
  /dev/sdc2  vg-demo lvm2 a--  4.11g 4.11g
```

**Bước 4: Tạo group volume**

Mục đích của bước này là nhóm các physic volume tạo được trong bước 3 thành một nhóm 

`vgcreate vg-demo /dev/sdb1 /dev/sdb2 /dev/sdc1 /dev/sdc2`

Trong đó vg-demo là tên của Volume Group.

Kiểm tra lại bằng lệnh vgdisplay.

```
root@toan:~# vgdisplay
  --- Volume group ---
  VG Name               vg-demo
  System ID
  Format                lvm2
  Metadata Areas        4
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                4
  Act PV                4
  VG Size               19.98 GiB
  PE Size               4.00 MiB
  Total PE              5116
  Alloc PE / Size       3072 / 12.00 GiB
  Free  PE / Size       2044 / 7.98 GiB
  VG UUID               jVFbTj-5qxV-B8Jh-d2BS-TWD0-H5q9-YQ3d1D
```

Trong đó trương VG Status có giá trị là resizable. 

**Bước 5: Tạo Logical Volume**

Mục đích của lệnh này là sau khi đã tạo được 1 nhóm (giống như gộp tất cả ổ đĩa thành 1) giờ ta chia ra thành các phân vùng theo ý của mình.

`lvcreate -L 8G -n lv-demo1 vg-demo`

Trong đó: 

* -L: Chỉ ra dung lượng của logical volume

* -n: Chỉ ra tên của logical volume. 

* lv-demo1 là tên Logical Volume, vg-demo là Volume Group mà mình vừa tạo ở bước trước.

* Lưu ý là chúng ta có thể tạo nhiều Logical Volume từ 1 Volume Group. 

Kiểm tra lại bằng lvdisplay

**Bước 6: Định dạng Logical Volume**

`mkfs -t ext4 /dev/vg-demo/lv-demo1`

Kiểm tra lại bằng lệnh `fdisk -f` nếu trường ftype của phân vùng lv-demo1 có kiểu ext4 là đúng.

**Bước 7: Mount và sử dụng**

Tạo thư mục demo1 `mkdir /home/anonymous/lvm-folder`

Tiến hành mount logical volume lv-demo1 vào thư mục demo1 như sau: `mount /dev/vg-demo/lv-demo1 /home/anonymous/lvm-folder`

Kiểm tra lại dung lượng của thư mục đã được mount: `df -h` Nếu thấy lv-demo1 được mount cho /home/anonymous/lvm-folder là đúng. 

	## 3.3. Thao tác chỉnh sửa
	
**Tăng kích thước Logical volume**

Trước khi tăng kích thước của 1 logic volume ta cần xem còn chỗ để tăng không bằng lệnh vgdisplay.

```
root@toan:~# vgdisplay
  --- Volume group ---
  VG Name               vg-demo
  System ID
  Format                lvm2
  Metadata Areas        4
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               0
  Max PV                0
  Cur PV                4
  Act PV                4
  VG Size               19.98 GiB
  PE Size               4.00 MiB
  Total PE              5116
  Alloc PE / Size       3072 / 12.00 GiB
  Free  PE / Size       2044 / 7.98 GiB
  VG UUID               jVFbTj-5qxV-B8Jh-d2BS-TWD0-H5q9-YQ3d1D
```

Nhìn từ kết quả có thể thấy VG status đang resizable tức là có thể có giãn được, và Alloc PE/Size = 3072/12GB tức là còn có thể tăng được gần 9GB nữa

`lvextend -L +5G /dev/vg-demo/lv-demo1`

* Dấu + mang ý nghĩa thêm vào volume lv-demo1 5GB

* Không có dấu + nghĩa là kích thước phân vùng là đúng 5GB.

Tuy nhiên, sau khi tăng kích thước cho Logical Volume thì Logical Volume đã được tăng nhưng file system trên volume này vẫn chưa thay đổi, bạn phải sử dụng lệnh sau để thay đổi:

`resize2fs /dev/vg-demo/lv-demo1`

Check lại bằng `df -h`

**Giảm kích thước logical volume**

Để giảm kích thước của Logical Volume, trước hết phải umount Logical Volume muốn giảm:

`umount /dev/vg-demo/lv-demo1`

`lvreduce -L -1G /dev/vg-demo/lv-demo1`

Trong ví dụ này, ta giảm lv-demo1 đi 1GB (nếu không có dấu trừ sẽ giảm kích thước volume xuống còn 1G)

```
root@toan:~# lvreduce -L -2G /dev/vg-demo/lv-demo1
  WARNING: Reducing active logical volume to 10.00 GiB
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce lv-demo1? [y/n]: y
  Reducing logical volume lv-demo1 to 10.00 GiB
  Logical volume lv-demo1 successfully resized
root@toan:~#
```

Sau đó tiến hành format lại Logical Volume:

`mkfs.ext4 /dev/vg-demo/lv-demo1`

Cuối cùng là mount lại Logical Volume:

`mount /dev/vg-demo/lv-demo1 demo1`


............to be continued.....









