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

# Cách cài đặt và sử dụng cơ bản

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
