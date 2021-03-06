# Lab 4: Thin Provisioning

## Mục tiêu

Hiểu được cơ chế của thin provisioning, bằng cách cấp 4LV có tổng kích thước lớn hơn khả năng của 3 PV cung cấp.

## Điều kiện.

1 máy ảo chạy ubuntu 16.04 có 2 ổ cứng.

Do tận dụng từ bài lab trước, ta cần format lại để các ổ đĩa như lúc ban đầu bằng cách umount các LV, remove VG. 

## Tiến hành

Như đã nói trong phần điều kiện, ta sẽ có 2 ổ cứng với 2 phân vùng sde1, sdf1 sẽ được add vào 1 vg tên là vg_thin 

Thực hiện tạo VG có tên vg_thin như lab1bbbbbbbb đã hướng dẫn.

Luôn kiểm tra dung lượng trước khi thực hiện

```
# vgs
  VG      #PV #LV #SN Attr   VSize  VFree
  vg_lvm    3   2   0 wz--n- 20.45g  7.45g
  vg_thin   2   5   0 wz--n- 14.99g 14.99g
```

Bước 1: Tạo ra một thin pool với câu lệnh như sau:

  `# lvcreate -l 3830 --thinpool volume_pool vg_thin`

Trong đó:

  - `-l 3830`: dùng để khai báo kích thước của thin pool sẽ tạo ra tính theo giá trị PE.
  - `--thinpool`: khai báo logical volume tạo ra thuộc kiểu `thin pool`
  - `volume_pool`: tên của thin pool sẽ tạo ra.
  - `vg_thin`: tên của Volume Group sẽ được sử dụng để tạo ra thin pool


Bước 2: Ta sẽ tạo ra các thin volume. 

Bản chất nó cũng là một LV chỉ khác nó nằm trong 1 pool các volume. Ta sẽ chỉ thấy kích thước
tối đa, nhưng không có nghĩa là nó sẽ chiếm lượng PE đúng bằng lượng PE ban đầu yêu cầu cấp phát, tạo ra tính động cho LVM.

  `# lvcreate -V 1024 --thin -n vol1 vg_thin/volume_pool`

trong đó:

  - `-V 1024`: khai báo kích thước của thin volume là 1024MB
  - `--thin`: Khai báo kiểu tạo ra volume là thin volume
  - `-n vol1`: khai báo tên của thin volume tạo ra là vol01
  - `vg_thin/volume_pool`: khai báo thin pool được sử dụng để tạo ra thin volume.

kết quả sẽ hiển thị tương tự như sau:

Using default stripesize 64.00 KiB.
Logical volume "vol1" created.

Tương tự như trên, ta hãy tạo ra thêm 2 thin pool lần lượt có tên là vol2 và vol3, vol4. Cuối cùng, ta có được thông tin như sau:

Do mỗi vol chiếm 1GB, ta sẽ tạo ra vol5 có kích thước >13GB để nhìn thấy cái hay của thin provisioning đó là cho phép tổng kích thước
các LV lớn hơn kích thước PVs.

```
# lvs
  LV          VG      Attr      LSize  Pool        Origin Data%  Move Log Copy%  Convert
  lv_addition vg_lvm  -wi-a----  3.00g
  lv_test     vg_lvm  -wi-ao--- 10.00g
  vol1        vg_thin Vwi-a-tz-  1.00g volume_pool          0.00
  vol2        vg_thin Vwi-a-tz-  1.00g volume_pool          0.00
  vol3        vg_thin Vwi-a-tz-  1.00g volume_pool          0.00
  vol4        vg_thin Vwi-a-tz-  1.00g volume_pool          0.00
```

Bước 3: Thực hiện mount các thin pool đã tạo ra vào hệ thống. Ta làm tương tự như khi mount một Logical Volume.

Tạo ra các thư mục có chức năng gắn kết tương ứng cho các thin pool:

  `# mkdir -p /mnt/{vol1,vol2,vol3,vol4}`

Format định dạng cho các thin pool:

```
  # mkfs -t ext4 /dev/vg_thin/vol1
  # mkfs -t ext4 /dev/vg_thin/vol2
  # mkfs -t ext4 /dev/vg_thin/vol3
  # mkfs -t ext4 /dev/vg_thin/vol4
```

Mount các thin pool đã tạo ra vào hệ thống ứng với các thư mục đã tạo ở phần đầu của bước này:

```
  # mount /dev/vg_thin/vol01 /mnt/vol1/
  # mount /dev/vg_thin/vol02 /mnt/vol2/
  # mount /dev/vg_thin/vol03 /mnt/vol3/
  # mount /dev/vg_thin/vol03 /mnt/vol4/
```

Bước 4: Tạo thêm LV

  `# lvcreate -V 14000 --thin -n vol5 vg_thin/volume_pool`

Kết quả sẽ hiển thị tương tự như sau:

Using default stripesize 64.00 KiB.
WARNING: Sum of all thin volume sizes (15 GB) exceeds the size of thin pool vg_thin/volume_pool and the size of whole volume group (3.98 GiB)!
For thin pool auto extension activation/thin_pool_autoextend_threshold should be below 100.
Logical volume "vol04" created.

Khi ta kiểm tra với câu lệnh lvs sẽ nhận được kết quả tương tự như sau:

```
# lvs
  LV          VG      Attr      LSize  Pool        Origin Data%  Move Log Copy%  Convert
  lv_addition vg_lvm  -wi-a----  3.00g
  lv_test     vg_lvm  -wi-ao--- 10.00g
  vol1        vg_thin Vwi-a-tz-  1.00g volume_pool          0.00
  vol2        vg_thin Vwi-a-tz-  1.00g volume_pool          0.00
  vol3        vg_thin Vwi-a-tz-  1.00g volume_pool          0.00
  vol4        vg_thin Vwi-a-tz-  1.00g volume_pool          0.00
  vol5        vg_thin Vwi-a-tz- 13.67g volume_pool          0.00
  volume_pool vg_thin twi-a-tz- 14.96g                      0.00
```

Kết quả trên khẳng định nhờ thin provisioning mà ta đã tận dụng được thêm phần dung lượng còn trống trên các LV đã được cấp, giúp tiết kiệm để làm việc khác.

Tiếp tục thực hiện các bước để có thể sử dụng thin volume vừa tạo ra tương tự như ở trên:

```
  # mkdir /mnt/vol5
  # mkfs.ext4 /dev/vg_thin/vol5
  # mount /dev/vg_thin/vol5 /mnt/vol5
```

Có một điều cần lưu ý khi ta bắt đầu sử dụng đến Over Provisioning đó là khi dữ liệu người dùng tăng đột xuất và sẽ sử dụng đầy đủ hết dung lượng được cấp.
 Thì sẽ xảy ra xung đột trong hệ thống, ta cần phải bổ sung dung lượng bộ nhớ cho thin pool kịp thời để tránh xảy ra xung đột. Hãy thực hiện, thêm dung lượng cho 
 thin pool bằng việc sử dụng câu lệnh lvextend và coi thin pool của chúng ra đã tạo như một Logical Volume thông thường. Ví dụ:

  `# lvextend -L +15G /dev/vg_thin/volume_pool`

**Tham khảo**

 [https://www.tecmint.com/setup-thin-provisioning-volumes-in-lvm/](https://www.tecmint.com/setup-thin-provisioning-volumes-in-lvm/)

 
