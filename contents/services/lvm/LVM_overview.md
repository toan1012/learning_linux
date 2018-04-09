#  LVM Overview

## LVM là gì?

LVM là viết tắt của cụm từ Logical Volume Manager.

Đây là một phương pháp cho phép chuyển các không gian đĩa cứng thành những logical volume làm cho việc thay đổi kích thước trở lên dễ dàng hơn (so với partition). Với kỹ thuật này,
 bạn có thể thay đổi kích của một hay nhiều disk mà không cần phải can thiệp đến quá nhiều về hệ thống. Có nghĩa là bạn có thay đổi một tùy ý cho dung lượng của phân vùng ổ 
 cứng trong hệ điều hành.

## Chức năng của LVM

Tạo và quản lý các Logical Volume. Một Logical Volume là một phân vùng được hình thành từ nhiều phân vùng ổ đĩa khác nhau để có dung lượng chứa bằng tổng các dung lượng 
của các ổ đĩa hình thành lên nó.

Hỗ trợ tính năng Snapshot, cho phép phục hổi hiện trạng của không gian ổ đĩa tại một thời điểm nào đó.

Cung cấp tính năng Thin Provisioning Volumes

Có thể quản lý nhiều Logical Volume cùng một lúc

Hỗ trợ tính năng LVM Migration.

## Cách cài đặt và sử dụng cơ bản

LVM là một phương pháp được hỗ trợ sẵn trong các hệ điều hành Linux. Các tính năng của nó được cung cấp qua nhiều tiện các câu lệnh. Ta sẽ cần phải nhớ một vài lưu ý sau,
trước khi bắt đầu thực hiện quản lý các logical volume, cũng như file system.

Đối với các thao tác về Physical Volumes. Ta sẽ sử dụng các câu lệnh chứa tiền tố pv* được hỗ trợ trong terminal của Linux. 

một Physical Volume là một đĩa vật lý, hoặc một phân vùng đĩa mà chúng ta sử dụng cho hệ điều hành chẳng hạn như /dev/sda1, /dev/sdb1, ... Mà khi ta muốn sử dụng chúng, 
ta chỉ cần thực hiện thao tác mount nó vào để sử dụng. LVM sử dụng chúng để tạo lên một Volume Group.

Đối với những các thao tác về Volume Group. Ta sẽ sử dụng các câu lệnh chứa tiền tố vg* được hỗ trợ trong terminal. 

một Volume Group như đã nói ở trên chính là một nhóm các Physical Volume hình thành lên. Có thể coi đây như là một phân vùng ảo.

Đối với các thao tác về Logical Volume. Ta sẽ sử dụng các câu lệnh có tiền tố lv* hỗ trợ trong termial. 

đây chính là những volume hoàn chỉnh ở mức cuối cùng trước khi có thể mount vào hệ điều hành. Ta có thể thay đổi, thêm bớt kích thước của những volume một cách nhanh chóng.
 Cho tới khi chúng được chứa trong các Volume Group, ta có thể cung cấp dung lượng cho chúng lớn hơn nhưng Physical Volume đơn. Ví dụ: Ban đầu, ta có 4 ổ cứng với dung lượng
  10GB. Thì ta có thể có một Logical Volume với bất kỳ dung lượng nào được cho với giá trị tối đa là 40GB, sau đó là những giá trị khác 20GB, 30GB hoặc 10GB.

Tóm lại, LVM có thể sử dụng các chức năng trên để sử dụng các kết hợp Physical Volume vào trong một Volume Group để thống nhất không gian lưu trữ có sẵn trên hệ thống. Sau đó,
 ta có thể chia chúng thành những phân vùng có kích thước khác nhau.

![Imgur](https://i.imgur.com/YgE2Ewj.png)

Cuối cùng, các bước để sử dụng LVM cho việc quản lý ổ đĩa như sau:

Bước 1: Tạo ra Physical Volumes từ không gian của một hay nhiều đĩa cứng.

Bước 2: Tạo ra Volume Groups từ không gian của các Physical Volume.

Bước 3: Tạo ra Logical Volume từ Volume Groups và sử dụng chúng.

## Về Snapshot

Snapshot trong LVM là một tính năng hữu ích cho phép tạo ra các bản sao lưu dữ liệu của một Logical Volume nào đó 

Snapshot cung cấp cho ta một tính năng phục hồi dữ liệu của một Logical Volume trước thời điểm tạo ra nó. 

Snapshot không phải là bản sao lưu đầy đủ. Tại mỗi thời điểm snapshot chương trình chỉ ghi lại những thay đổi so với lần snapshot trước. Điều ấy lý giải 1 phần câu hỏi: 
"Tại sao bản snapshot có dung lượng nhẹ hơn so với bản gốc và bản backup"

## Về Migrate

Backup là một chức năng cho phép lưu lại toàn bộ trạng thái tại thời điểm backup của 1 phân vùng, 1 LV, hay cả 1 đĩa vật lý. Giữa 2 lần backup không có liên quan gì tới nhau
Dữ liệu backup sẽ lớn hơn nhiều so với snapshot.  Đây cũng là điểm khác nhau cơ bản của backup và snapshot.

Migrate đã làm tốt vai trò backup khi nó có thể tạo ra một bản sao dữ liệu từ một Logical Volume này đến một ổ đĩa mới mà không làm mất dữ liệu cũng như xảy ra tình trạng downtime.

Mục đích của tính năng này đó là di chuyển dữ liệu của chúng ta từ một đĩa cứng cũ đến một đĩa cứng mới. Điều này được thực hiện khi mà trên đĩa hiện đang lưu dữ liệu phát sinh
ra một số lỗi.

## Về Thin Provisioning

Trong LVM, chúng ta được cung cấp một tính năng khá là tuyệt vời tương tự như tính năng Snapshot - đó là tính năng Thin Provisioning. Vậy tính năng này có thể làm được những gì 
mà được cho là một tính năng tuyệt vời?

Thin Provisioning là một tính năng được sử dụng để tạo ra những ổ đĩa ảo từ những storage pool. Hãy thử giả định rằng, ta đang có một storage pool với dung lượng 15Gb. Ta đang 
sử dụng nó để cung cấp không gian lưu trữ cho 3 người, với mỗi người có dung lượng lưu trữ tối đa là 5Gb cho mỗi storage pool của mỗi người. Và thực tế, 3 người họ chỉ sử dụng
 hết 6Gb trong tổng số 15Gb. Như vậy. ta đang còn trống 9Gb dung lượng từ storage pool ban đầu.

Khi ta sử dụng tính năng Thin Provisioning để cung cấp dung lượng lưu trữ cho 3 người kia một storage pool, mỗi pool lúc này sẽ được xem là một thin pool. Tất cả những gì về dữ 
liệu của bạn sẽ vẫn được lưu trữ và không gian lưu trữ sẽ vẫn hiển thị với không gian là 5Gb. Nhưng thực tế, hiện tại, bạn sẽ không được cung cấp đầy đủ dung lượng lưu trữ là 5Gb.
 Chính vì điều này, khi chúng ta giả định rằng, 3 người kia được cung cấp không gian lưu trữ là 5Gb và họ chỉ sử dụng hết 6Gb trong tổng số 15Gb ban đầu của chúng ta thì chúng ta vẫn 
 có thể cung cấp một không gian lưu trữ khác cũng với 5Gb dung lượng cho một người nữa. Như vậy, ta có thể cung cấp được dung lượng bộ nhớ lưu trữ cho ít nhất 3 người với mỗi 
 không gian nhớ là 5Gb. Đây chính là tính năng mà Thin Provisioning hướng đến để giải quyết.
 
 Nói tóm lại, sử dụng Thin Provisioning để nâng hiệu suất sử dụng không gian lưu trữ, để tiết kiệm bộ nhớ. Tuy nhiên nếu tất cả các ổ LV đều đầy thì hệ thống treo do không thể đọc
 ghi được nữa, nên vấn đề mở rộng dung lượng cần được quan tâm. 
 
 Tham khảo
 
 [https://www.tecmint.com/setup-thin-provisioning-volumes-in-lvm/](https://www.tecmint.com/setup-thin-provisioning-volumes-in-lvm/)

[https://www.tecmint.com/create-lvm-storage-in-linux/](https://www.tecmint.com/create-lvm-storage-in-linux/)

[https://www.tecmint.com/take-snapshot-of-logical-volume-and-restore-in-lvm/](https://www.tecmint.com/take-snapshot-of-logical-volume-and-restore-in-lvm/)
 
 
