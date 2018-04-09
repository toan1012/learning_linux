# Lab3: Snapshot

## Mục tiêu.

* Tạo được bản snapshot của 1 LV (lv_addition)

* Convert snapshot.

## Điều kiện

Vẫn sử dụng điều kiện từ lab2. Nhớ lại là ta đang có 2 LVs: lv_test và lv_addition.

Ta sẽ mount lv_addition vào 1 thư mục rồi ghi lên trên đó 1 file test.txt với nội dung: "Test file snapshot". Sau đó tiến hành snapshot sang 1 PV có tên lv_add_snap. 
Thử mount file snapshot vào 1 thư mục rồi đọc nội dung. 

## Tiến hành

Trước khi làm gì cũng phải nhớ kiểm tra dung lượng VG. Trong trường hợp này VG chỉ cần <3GB là đủ.  Điểm lưu ý là dung lượng của snapshot nhỏ hơn hoặc bằng dung lượng
của Volume gốc. Nó lưu lại những thay đổi của LV chứ không phải lưu lại cả LV nên kích thước hầu hết nhỏ hơn của LV. Câu hỏi đặt ra là dung lượng bao nhiêu là đủ? Sẽ 
cần thêm thời gian để tìm hiểu.

```
# vgs
  VG     #PV #LV #SN Attr   VSize  VFree
  vg_lvm   3   2   0 wz--n- 20.45g 7.45g
```

VG còn rảnh 7.45GB thoải mái để tạo LV snapshot. 

Trước tiên ta sẽ tạo 2 file test.txt và error.txt ở /tmp/lvm để ghi 2 file này lên lv_addition là file gốc sẽ snapshot. Dùng lệnh 

`touch error.txt test.txt`

2 file này sinh ra với vai trò để theo dõi. 

```
# lvcreate -L 3G --snapshot -n lv_add_snap /dev/vg_lvm/lv_addition
  Logical volume "lv_add_snap" created
# lvs
  LV          VG     Attr      LSize  Pool Origin      Data%  Move Log Copy%  Convert
  lv_add_snap vg_lvm swi-a-s--  3.00g      lv_addition   0.00
  lv_addition vg_lvm owi-aos--  3.00g
  lv_test     vg_lvm -wi-ao--- 10.00g
```

Đoạn code trên đã tạo được 1 LV snapshot trên đó có nội dung của 2 file error.txt và test.txt` Trong đó, `--snapshot` có thể thay bằng
`-s`. 

Xóa các file .txt vừa tạo đi rồi tiến  hành backup để xem thử nó có lưu lại trạng thái trước khi xóa 2 file .txt không.

Back up ta dùng lệnh 

```
# lvconvert --merge /dev/vg_lvm/lv_add_snap
  Merging of volume lv_add_snap started.
  lv_addition: Merged: 100.0%
  Merge of snapshot into logical volume lv_addition has finished.
  Logical volume "lv_add_snap" successfully removed
```

Để dùng được ta mount lại vào thư mục /tmp/lvm. Sau khi lệnh trên chạy, lv_add_snap sẽ mất.

Sau đó check /tmp/lvm và đã thấy 2 file xóa đi, nay được backup lại

Có một lỗi xảy ra trong quá trình umount LV lv_addition đó là 

```
umount -f /tmp/lvm
umount2: Device or resource busy
umount: /tmp/lvm: device is busy.
        (In some cases useful info about processes that use
         the device is found by lsof(8) or fuser(1))
umount2: Device or resource busy
```

Gặp tình huống này ta sẽ dùng lệnh `lsof /tmp/lvm/` để tìm tiến trình đang đọc ghi, rồi xóa hết đi bằng lệnh `kill -9 id_tiến_trình`

Một lưu ý nữa là snapshot LV cũng được xem là một LV bình thường nên mọi lệnh về tăng giảm kích thước của LV đều làm được với snapshot. 
 
**Tham khảo**

https://www.tecmint.com/take-snapshot-of-logical-volume-and-restore-in-lvm/

http://oletange.blogspot.com/2012/04/umount-device-is-busy-why.html

