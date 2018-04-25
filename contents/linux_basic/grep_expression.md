# Lệnh grep kết hợp expression

## Basic 

Có 1 dir chứa toàn bộ giấy phép trên ubuntu /usr/share/common-licenses

```
cd /usr/share/common-licenses
grep "GNU" GPL-3
```
Lệnh trên chỉ ra các từ GNU trong file. các từ này chính phải chính xác. 

Lệnh dưới đây sẽ chấp nhận cả viết hoa, thường, hỗn hợp do -i nghĩa là ignore-case

`grep -i "license" GPL-3`

Ngược lại, match với dòng không có từ nào đó ta dùng thuộc tính -v nghĩa là --invert-match

## Expression

Để match một cụm từ ở đầu dòng dùng anchor "^" ở trước từ đấy. Ví dụ:

`grep "^GNU" GPL-3`

Lệnh trên tìm tất cả các dòng có GNU đứng đầu dòng. 

Để hiện dòng mà kết quả trả ra có thể dùng thêm option -n.






## Tham khảo

[link grep ](https://www.digitalocean.com/community/tutorials/using-grep-regular-expressions-to-search-for-text-patterns-in-linux)
