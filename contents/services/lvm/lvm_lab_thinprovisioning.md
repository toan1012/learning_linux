# Lab 4: Thin Provisioning

## Mục tiêu

Hiểu được cơ chế của thin provisioning, bằng cách cấp 4LV có tổng kích thước lớn hơn khả năng của 3 PV cung cấp.

## Điều kiện.

1 máy ảo chạy ubuntu 16.04 có 3 ổ cứng.

Do tận dụng từ bài lab trước, ta cần format lại để các ổ đĩa như lúc ban đầu bằng cách umount các LV, remove VG. 

## Tiến hành

Như đã nói trong phần điều kiện, ta sẽ có 3 ổ cứng với 3 phân vùng sdb1, sdc1, sdd1 sẽ được add vào 1 vg tên là vg_thin 

Đầu tiên, ta hãy đổi tên của vg_lvm thành vg_thin cho dễ dàng phân biệt với các Volume Group khác (nếu có.)

  # vgrename vg_lvm vg_thin

kết quả sẽ hiển thị:

Volume group "vg_lvm" successfully renamed to "vg_thin"

cuối cùng, trước khi bắt đầu thực hiện sử dụng tính năng Thin Provisioning ta có dữ liệu như sau:

  # vgs
VG   #PV #LV #SN Attr   VSize  VFree
vg_thin   4   0   0 wz--n-  3.98g 3.98g

Để thực hiện sử dụng tính năng Thin Provisioning ta làm như sau:

Bước 1: Tạo ra một thin pool với câu lệnh như sau:

  # lvcreate -L 1018 --thinpool vol_pool vg_thin

trong đó:

  - `-l 1018G`: dùng để khai báo kích thước của thin pool sẽ tạo ra là 3.98Gb tính theo giá trị PE.
  - `--thinpool`: khai báo logical volume tạo ra thuộc kiểu `thin pool`
  - `vol_pool`: tên của thin pool sẽ tạo ra.
  - `vg_thin`: tên của Volume Group sẽ được sử dụng để tạo ra thin pool

kết quả sẽ hiển thị tương tự như sau:

  # lvcreate -l 1018 --thinpool vol_pool vg_thin

Using default stripesize 64.00 KiB.
Logical volume "vol_pool" created.
  
  # lvs

LV VG   Attr   LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
vol_pool vg_thin twi-a-tz--  3.98g 0.00   1.17
root   cl   -wi-ao---- 17.00g
swap   cl   -wi-ao----  2.00g

Bước 2: Ta sẽ tạo ra một thin volume. Cách làm như sau:

  # lvcreate -V 1024 --thin -n tv_client01 vg_thin/vol_pool

trong đó:

  - `-V 1024`: khai báo kích thước của thin volume là 1024Mb
  - `--thin`: Khai báo kiểu tạo ra volume là thin volume
  - `-n tv_client01`: khai báo tên của thin volume tạo ra là tv_client01
  - `vg_thin/vol_pool`: khai báo thin pool được sử dụng để tạo ra thin volume.

kết quả sẽ hiển thị tương tự như sau:

Using default stripesize 64.00 KiB.
Logical volume "tv_client01" created.

  # lvs
LV VG   Attr   LSize  Pool   Origin Data%  Meta%  Move Log Cpy%Sync Convert
vol_pool vg_thin twi-aotz--  3.98g   0.00   1.27
tv_client01vg_thin Vwi-a-tz--  1.00g vol_pool0.00
root   cl   -wi-ao---- 17.00g
swap   cl   -wi-ao----  2.00g

tương tự như trên, ta hãy tạo ra thêm 2 thin pool lần lượt có tên là tv_client02 và tv_client03. Cuối cùng, ta có được thông tin như sau:

  # lvs

LV VG   Attr   LSize  Pool   Origin Data%  Meta%  Move Log Cpy%Sync Convert
vol_pool vg_thin twi-aotz--  3.98g   0.00   1.46
tv_client01vg_thin Vwi-a-tz--  1.00g vol_pool0.00
tv_client02vg_thin Vwi-a-tz--  1.00g vol_pool0.00
tv_client03vg_thin Vwi-a-tz--  1.00g vol_pool0.00
root   cl   -wi-ao---- 17.00g
swap   cl   -wi-ao----  2.00g

Bước 3: Thực hiện mount các thin pool đã tạo ra vào hệ thống. Ta làm tương tự như khi mount một Logical Volume.

Tạo ra các thư mục có chức năng gắn kết tương ứng cho các thin pool:

  # mkdir -p /mnt/{client01,client02,client03}

Format định dạng cho các thin pool:

  # mkfs.ext4 /dev/vg_thin/tv_client01
  # mkfs.ext4 /dev/vg_thin/tv_client02
  # mkfs.ext4 /dev/vg_thin/tv_client03

Mount các thin pool đã tạo ra vào hệ thống ứng với các thư mục đã tạo ở phần đầu của bước này:

  # mount /dev/vg_thin/tv_client01 /mnt/client01/
  # mount /dev/vg_thin/tv_client02 /mnt/client02/
  # mount /dev/vg_thin/tv_client03 /mnt/client03/

Bước 4: Thực hiện kiểm tra dung lượng thực sự đã sử dụng của thin pool:

  # lvs

kết quả hiện thị tương tự như sau:

LV VG   Attr   LSize  Pool   Origin Data%  Meta%  Move Log Cpy%Sync Convert
vol_pool vg_thin twi-aotz--  3.98g   3.61   3.22
tv_client01vg_thin Vwi-aotz--  1.00g vol_pool4.79
tv_client02vg_thin Vwi-aotz--  1.00g vol_pool4.79
tv_client03vg_thin Vwi-aotz--  1.00g vol_pool4.79
root   cl   -wi-ao---- 17.00g
swap   cl   -wi-ao----  2.00g

theo kết quả trên, ta thấy, các thin volume hiện đan sử dụng 4.79% dung lượng và thin pool chỉ sử dụng 3.61%.

4.3 Tính năng Over Provisioning

Như ở phần đầu của bài viết đã đề cập. Khi sử dụng tính năng Thin Provisioning ta có thể cung cấp dung lượng cho nhiều hơn những gì thực tế ta có khi storage pool hiện đang còn có dung lượng trống. Việc cung cấp như này được gọi là Over Provisioning.

Ngay bây giờ, ta sẽ thự hiện quá trình Over Provisioning bằng việc tạo thêm một thin volume 2Gb nữa. Vì ta đang có 3 thin volume sử dụng hết 3.16% trong tổng dung lượng của thin pool đã tạo ra.

  # lvcreate -V 2048 --thin -n tv_client04 vg_thin/vol_pool

kết quả sẽ hiển thị tương tự như sau:

Using default stripesize 64.00 KiB.
WARNING: Sum of all thin volume sizes (5.00 GiB) exceeds the size of thin pool vg_thin/vol_pool and the size of whole volume group (3.98 GiB)!
For thin pool auto extension activation/thin_pool_autoextend_threshold should be below 100.
Logical volume "tv_client04" created.

Khi ta kiểm tra với câu lệnh lvs sẽ nhận được kết quả tương tự như sau:

LV VG   Attr   LSize  Pool   Origin Data%  Meta%  Move Log Cpy%Sync Convert
vol_pool vg_thin twi-aotz--  3.98g   3.61   3.32
tv_client01vg_thin Vwi-aotz--  1.00g vol_pool4.79
tv_client02vg_thin Vwi-aotz--  1.00g vol_pool4.79
tv_client03vg_thin Vwi-aotz--  1.00g vol_pool4.79
tv_client04vg_thin Vwi-a-tz--  2.00g vol_pool0.00
root   cl   -wi-ao---- 17.00g
swap   cl   -wi-ao----  2.00g

Tiếp tục thực hiện các bước để có thể sử dụng thin volume vừa tạo ra tương tự như ở trên:

  # mkdir /mnt/client04
  # mkfs.ext4 /dev/vg_thin/tv_client04
  # mount /dev/vg_thin/tv_client04 /mnt/client04

khi kiểm tra câu lệnh df -H ta được:

  /dev/mapper/cl-root19G  1.3G   17G   8% /
  devtmpfs  945M 0  945M   0% /dev
  tmpfs 957M 0  957M   0% /dev/shm
  tmpfs 957M  9.1M  947M   1% /run
  tmpfs 957M 0  957M   0% /sys/fs/cgroup
  /dev/sda1 1.1G  145M  919M  14% /boot
  tmpfs 192M 0  192M   0% /run/user/1000
  /dev/mapper/vg_thin-tv_client01  1.1G  2.7M  951M   1% /mnt/client01
  /dev/mapper/vg_thin-tv_client02  1.1G  2.7M  951M   1% /mnt/client02
  /dev/mapper/vg_thin-tv_client03  1.1G  2.7M  951M   1% /mnt/client03
  /dev/mapper/vg_thin-tv_client04  2.1G  6.3M  2.0G   1% /mnt/client04

Có một điều cần lưu ý khi ta bắt đầu sử dụng đến Over Provisioning đó là khi dữ liệu người dùng tăng đột xuất và sẽ sử dụng đầy đủ hết 5Gb. Thì sẽ xảy ra xung đột trong hệ thống, ta cần phải bổ sung dung lượng bộ nhớ cho thin pool kịp thời để tránh xảy ra xung đột. Hãy thực hiện, thêm dung lượng cho thin pool bằng việc sử dụng câu lệnh lvextend và coi thin pool của chúng ra đã tạo như một Logical Volume thông thường. Ví dụ:

  # lvextend -L +15G /dev/vg_thin/vol_pool

