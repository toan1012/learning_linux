# Lab 5: Migrate LVM

#1

## Mục tiêu#1

Migrate LV sang 1 LV khác trên cùng 1 máy. 

## Điều kiện#1

Thực hiện trên ubuntu 16.04LTS.

Máy ảo có 2 ổ cứng. 

## Thực hiện#1

Bước 1: Kiểm tra ổ đĩa bằng lệnh pvs, kiểm tra VG bằng vgs

Bước 2, ta sẽ tạo ra các Physical Volume từ hai đĩa /dev/sdb1 và /dev/sdb2 như sau:

```
# pvcreate /dev/sdb[1-2]`
Physical volume "/dev/sdb1" successfully created.
Physical volume "/dev/sdb2" successfully created.
```

Bước 3, ta sẽ tạo ra một Volume Group từ hai Physical Volume đã tạo ra:

  `# vgcreate vg_mig /dev/sdb[1-2]`

Bước thứ 5, tạo ra một Mirror Volume từ Volume Group vừa tạo với câu lệnh:

  `# lvcreate -L 5G -m 1 -n vg_mig_mirror vg_mig`

trong đó:

  - `vg_mig_mirror`: là tên của `Logical Volume` sẽ tạo ra
  - `vg_mig`: là tên của `Volume Group` sử dụng để tạo `Logical Volume`
  - `-L 5G`: Khai báo dung lượng của `Logical Volume` sẽ tạo ra là 5Gb
  - `-m 1`: Khai báo số bản sao lưu của dữ liệu. Ở đây là 1 (nhưng sẽ tạo ra 2).

Bước 4, ta sẽ tạo ra một filesystem cho Mirrored Volume vừa tạo và thực hiện mount nó vào thư mục /mnt/Mirror:

```
  # mkfs -t ext4 /dev/vg_mig/vg_mig_mirror

  # mkdir /mnt/Mirror

  # mount /dev/vg_mig/vg_mig_mirror /mnt/Mirror
```

như vậy, ta đã tạo và sử dụng thành công Mirror Volume.


Như vậy ta thấy được, dữ liệu của Mirror Volume đang được lưu trên 2 đĩa. Như vậy, ta có thể tùy ý xóa đi một bản sao của Volume mà không lo đến mất dữ liệu. 
Để thực hiện xóa đi một Mirrored Volume, ta sử dụng câu lệnh:

   `lvconvert -m 0 /dev/vg_mig/LVM_LV_Mirror /dev/sdb1`

trong đó:

  - `-m 0`: Khai báo chuyển đổi từ `Mirror Volume` sang `Linear Volume` (xóa đi)
  - `/dev/sdb1`: Khai báo tên của thiết bị sẽ bị xóa `Mirrored Volume`
  - `/dev/vg_mig/LVM_LV_Mirror`: Khai báo tên của `Mirror Volume`

Kiểm tra lại với câu lệnh lvs -o+devices -a, ta có kết quả tương tự như sau:

```
LVVG  Attr   LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert Devices
LVM_LV_Mirror vg_mig -wi-ao----  5.00g /dev/sdb2(1)
root  cl  -wi-ao---- 17.00g /dev/sda2(512)
swap  cl  -wi-ao----  2.00g /dev/sda2(0)
```

như trên ta thấy, LVM_LV_Mirror đang sử dụng mỗi /dev/sdb2 để lưu trữ dữ liệu. Ta thử kiểm tra lại thông tin của file test.txt đã tạo và thấy dữ liệu vẫn vẹn toàn.

```
  # cat test.txt
```


