# Lab2: Thay đổi kích thước logical volume

## Muc tiêu 

* Mục tiêu của lab này sẽ giúp ta hiểu về quá trình cấp phát dung lượng cho LV trong Lab1 ta đã tạo ra. Theo logic thông thường ta
biết nếu 1 đĩa cứng cấp dung lượng cho 1 phân vùng gần như hết dung lượng nó có thì lẽ dĩ nhiên ta không thể cấp được nữa. Nghĩa là muốn cấp thêm
chỉ có cách tăng thêm số lượng đĩa cứng. Với LVM, việc tăng thêm này sẽ đơn giản hơn nhờ khả năng mở rộng linh hoạt chỉ bằng lệnh vgextend.

* Hiểu được cách cấp phát của LVM. LVM giống như người chủ buôn bán chung cư mà đĩa cứng giống như 1 tòa chung cư trong quyền quản lý.
Người tới mua chỉ đến báo cần mua vài căn nhà trong chung cư. Ông chủ tòa chung cư sẽ bán cho 1 số chung cư có kích thước bằng nhau. Mỗi căn hộ ấy 
giống như 1 PE mà LVM cấp. Mặc định mỗi PE này có kích thước 4MB.

* Tạo thêm 1 LV 2GB từ vg_lvm.

## Điều kiện

Điều kiện của lab này tiếp nối so với lab 1. ta có thể nhìn thấy thông qua lệnh 

```
# vgdisplay
  --- Volume group ---
  VG Name               vg_lvm
  System ID
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  2
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               10.45 GiB
  PE Size               4.00 MiB
  Total PE              2676
  Alloc PE / Size       2560 / 10.00 GiB
  Free  PE / Size       116 / 464.00 MiB
  VG UUID               8Vdw0a-TJIn-WzsZ-535s-9Hd5-f8Qw-CrPFVG
```

Tông số PE (số phòng trong chung cư ) mà VG có tên vg_lvm đang quản lý là 2676 trong đó đã cấp 2560PE cho 1VG. Còn 116PE tương ứng với 464MB có thể cấp.
Yêu cầu đặt ra là 2GB cho 1 LV nhưng nay còn 464MB nên đầu tiên cần nâng dung lượng của vg_lvm. rồi mới cấp cho LV mới. Đó là tinh thần của bài lab này.

## Thực hiện

Bước 1: Mở rộng kích thước vg_lvm

Để mở rộng kích thước vg_lvm ta thêm 1 ổ cứng mới sdd dung lượng 2GB, tạo phân vùng sdd1. Các bước này làm giống với sdb, sdc đã nói trong BBBBBBBBBBBBBBBBB bài trước

Thêm vào VG phân vùng sdd1 bằng cách. 

```
# vgextend vg_lvm /dev/sdd1
  No physical volume label read from /dev/sdd1
  Physical volume "/dev/sdd1" successfully created
  Volume group "vg_lvm" successfully extended
```

Kiểm tra lại để thấy sự thay đổi.

```
# vgdisplay
  --- Volume group ---
  VG Name               vg_lvm
  System ID
  Format                lvm2
  Metadata Areas        3
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                3
  Act PV                3
  VG Size               20.45 GiB
  PE Size               4.00 MiB
  Total PE              5235
  Alloc PE / Size       2560 / 10.00 GiB
  Free  PE / Size       2675 / 10.45 GiB
  VG UUID               8Vdw0a-TJIn-WzsZ-535s-9Hd5-f8Qw-CrPFVG
```

Nhìn vào các trường VSize, PE Size, Total PE, Free PE/ Size, Cur PV, Act PV đều tăng bằng lượng sdd1 thêm vào.
Thông số VG Status có giá trị resizable, giờ thì thoải mái thêm phân vùng LV rồi. 

Việc thêm một LV cũng như ta đã làm trước kia. 

```
# lvcreate -L 2G -n lv_addition vg_lvm
  Logical volume "lv_addition" created
```

lv_addition  đã được tạo. nếu cần sử dụng nó hãy nhớ format bằng định dạng (vd: ext4) bằng `mkfs` và mount vào FHS. Tôi đang mount ở /tmp/lvm 

## Bước 3: Thêm bớt dung lượng cho LV.

Xuất phát từ thực tế, một LV được gắn vào 1 thư mục, sau 1 thời gian, thư mục sắChỉ chấp nhận thời gian downtime ngắn.

Nguyên tắc thực hiện là : umount LV khỏi FHS => tăng bằng lvextend hoặc giảm bằng lvreduce => resize lại bằng resize2fs=> mount lại vào FHS.

Để quan sát LV ta còn có thể dùng lệnh `lvmdiskscan` 

```
# umount /dev/vg_lvm/lv_addition
#lvextend -L +2G /dev/vg_lvm/lv_addition
  Extending logical volume lv_addition to 4.00 GiB
  Logical volume lv_addition successfully resized
```

Chú ý là +2GB nghĩa là tăng 2GB cho LV, nếu quên dấu "+" thì LV sẽ chỉ có đúng 2GB.
Để kiểm tra lệnh umount có thể dùng lệnh `df -h` nếu không thấy LV nữa thì đúng.

Bây giờ ta sẽ giảm dung lượng của lv_addition 1GB. 





lvextend -l +256 /dev/LVMVolGroup/Private

trong đó /dev/LVMVolGroup/Private là Logical cần tăng kích thước.

kết quả nhận được sẽ tương tự như sau:

Size of logical volume LVMVolGroup/Private changed from 1008.00 MiB (252 extents) to 1.98 GiB (508 extents).
Logical volume LVMVolGroup/Private successfully resized.

Bước 3. Thực hiện tăng kích thước (Logical Volume phải đang được mount). Ta làm như sau:

```
# resize2fs /dev/vg_lvm/lv_addition
resize2fs 1.42.9 (4-Feb-2014)
Please run 'e2fsck -f /dev/vg_lvm/lv_addition' first.
# e2fsck -f /dev/vg_lvm/lv_addition
e2fsck 1.42.9 (4-Feb-2014)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/vg_lvm/lv_addition: 11/131072 files (0.0% non-contiguous), 25388/524288 blocks
# resize2fs /dev/vg_lvm/lv_addition
resize2fs 1.42.9 (4-Feb-2014)
Resizing the filesystem on /dev/vg_lvm/lv_addition to 1048576 (4k) blocks.
The filesystem on /dev/vg_lvm/lv_addition is now 1048576 blocks long.
```

Sau bước này ta mount lại vào thư mục cũ. Nhưng để lab phần tiếp theo tôi sẽ không làm thế. 

## Bước 4. Thực hiện giảm kích thước LV

Các bước giống như tăng chỉ thay câu lệnh `lvextend` thành `lvreduce`
```
# lvreduce -L -1G /dev/vg_lvm/lv_addition
  WARNING: Reducing active logical volume to 3.00 GiB
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce lv_addition? [y/n]: y
  Reducing logical volume lv_addition to 3.00 GiB
  Logical volume lv_addition successfully resized
```

Sau bước này nhớ resize và mount LV lại vào FHS để sử dung.

Đến đây là hoàn thành lab2. 


**Tham khảo**

[https://www.tecmint.com/extend-and-reduce-lvms-in-linux/](https://www.tecmint.com/extend-and-reduce-lvms-in-linux/)