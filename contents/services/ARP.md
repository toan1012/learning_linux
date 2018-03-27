# Tổng hợp kiến thức ARP 

## 1. Giới thiệu giao thức ARP

ARP là phương thức phân giải địa chỉ giữa địa chỉ lớp 3 và địa chỉ lớp 2 trong mô hình OSI. Quá trình thực hiện bằng cách: một thiết bị IP trong mạng gửi một gói tin local broadcast 
đến toàn mạng yêu cầu thiết bị khác gửi trả lại địa chỉ phần cứng (địa chỉ lớp datalink ) hay còn gọi là Mac Address của mình.

## 2. Cấu trúc bản tin ARP

Cấu trúc bản tin ARP được mô tả như hình sau:

!image[](https://pasteboard.co/HdRhBRv.png)

**Hardware type**:

* Xác định kiểu bộ giao tiếp phần cứng cần biết.

* Xác định với kiểu Ethernet giá trị 1.

**Protocol type**:

* Xác định kiểu giao thức cấp cao (layer 3) máy gửi sử dụng để giao tiếp.

* Giao thức dành cho IP có giá trị 0x0800.

**Hardware address length**: Xác định độ dài địa chỉ vật lý (tính theo đơn vị byte). Địa chỉ MAC nên giá trị của nó sẽ là 6.

**Protocol address length** : Xác định độ dài địa chỉ logic được sử dụng ở tầng trên (layer 3). Tùy thuộc vào IP sử dụng mà có giá trị khác nhau, hiện tại IPv4 được sử dụng rộng rãi
	nên trường này sẽ có giá trị là 4 (byte).

**Operation code** : Xác định loại bản tin ARP mà máy gửi gửi. Có một số giá trị phổ biến:

* 1 : bản tin ARP request.

* 2 : bản tin ARP rely.

* 3 : bản tin RARP request.

* 4 : bản tin RARP reply.

**Sender hardware address (SHA)**: Xác định địa chỉ MAC máy gửi.

* Trong bản tin ARP request: trường này xác định địa chỉ MAC của host gửi request.

* Trong bản tin ARP reply: trường này xác định địa chỉ MAC của máy host mà máy gửi bên trên muốn tìm kiếm.

**Sender protocol address (SPA)**: Xác định địa chỉ IP máy gửi.

**Target hardware address (THA)**: Xác định địa chỉ MAC máy nhận mà máy gửi cần tìm.

* Trong bản tin ARP request: Trường này chưa được xác định (nên sẽ để giá trị là: 00:00:00:00:00:00)

Trong bản tin ARP reply: Trường này sẽ điền địa chỉ của máy gửi bản tin ARP request.

**Target protocol address (PTA)**: Xác định địa chỉ IP máy gửi (máy cần tìm).


## 3.Cách thức hoạt động của ARP

**3.1. Hoạt động của ARP trong mạng LAN**

!image[](https://pasteboard.co/HdRmqHp.png)

Bước 1: Máy gửi kiểm tra cache của mình. Nếu đã có thông tin về sự ánh xạ giữa địa chỉ IP và địa chỉ MAC thì chuyển sang Bước 7.

Bước 2: Máy gửi khởi tạo gói tin ARP request với địa chỉ SHA và SPA là địa chỉ của nó, và địa chỉ TPA là địa chỉ IP của máy cần biết MAC. 
(Trường THA để giá trị toàn 0 để biểu hiện là chưa tìm được địa chỉ MAC)

Bước 3: Gửi quảng bá gói tin ARP trên toàn mạng (Địa chỉ MAC đích của gói tin Ethernet II là địa chỉ MAC quảng bá ff:ff:ff:ff:ff:ff).

Bước 4: Các thiết bị trong mạng đều nhận được gói tin ARP request. Gói tin được xử lý bằng cách các thiết bị đều nhìn vào trường địa chỉ Target Protocol Address.

Các thiết bị không trùng địa chỉ TPA thì hủy gói tin.

Thiết bị với IP trùng với IP trong trường Target Protocol Address sẽ bắt đầu quá trình khởi tạo gói tin ARP Reply bằng cách lấy các trường 
	Sender Hardware Address và Sender Protocol Address trong gói tin ARP nhận được đưa vào làm Target trong gói tin gửi đi. Đồng thời thiết bị 
	sẽ lấy địa chỉ MAC của mình để đưa vào trường Sender Hardware Address. Đồng thời cập nhất giá trị ánh xạ địa chỉ IP và MAC của máy gửi vào 
	bảng ARP cache của mình để giảm thời gian xử lý cho các lần sau.

Bước 5: Thiết bị đích bắt đầu gửi gói tin Reply đã được khởi tạo đến thiết bị nguồn vừa gửi bản tin ARP request. Gói tin reply là gói tin gửi unicast.

Bước 6: Thiết bị nguồn nhận được gói tin reply và xử lý bằng cách lưu trường Sender Hardware Address trong gói reply như địa chỉ phần cứng của thiết bị đích cần tìm.

Bước 7: Thiết bị nguồn update vào ARP cache của mình giá trị tương ứng giữa địa chỉ IP và địa chỉ MAC của thiết bị đích. Lần sau sẽ không còn cần tới ARP request.

**3.2. Hoạt động của ARP trong môi trường liên mạng**

!image[](https://pasteboard.co/HdRo9b9.png)

!image[](https://pasteboard.co/HdRoJ8N.png)

Hoạt động của ARP trong một môi trường phức tạp hơn đó là hai hệ thống mạng gắn với nhau thông qua một Router.

* Máy A thuộc mạng A muốn gửi gói tin tới máy B thuộc mạng B. 2 mạng này kết nối với nhau thông qua router C.

* Do các broadcast lớp MAC không thể truyền qua Router nên khi đó máy A sẽ xem Router C như một cầu nối hay một trung gian (Agent) để truyền dữ liệu
Trước đó, máy A sẽ biết được địa chỉ IP của Router C (địa chỉ Gateway) và biết được rằng để truyền gói tin tới B phải đi qua C.

* Để tới được router C thì máy A phải gửi gói tin tới port X của router C (là gateway trong LAN A). Quy trình truyền dữ liệu được mô tả như sau:

    * Máy A gửi ARP request để tìm MAC của port X.

    * Router C trả lời, cung cấp cho A địa chỉ MAC của port X.

    * Máy A truyền gói tin tới port X của router C (với địa chỉ MAC đích là MAC của port X, IP đích là IP máy B).

    * Router C nhận được gói tin của A, forward ra port Y. Trong gói tin có chứa địa chỉ IP máy B, router C sẽ gửi ARP request để tìm MAC của máy B.

    * Máy B sẽ trả lời router C MAC của mình, sau đó router sẽ gửi gói tin của A tới B.

Trên thực tế ngoài dạng bảng định tuyến này người ta còn dùng phương pháp proxy ARP (sẽ tìm hiểu phần sau), trong đó có một thiết bị đảm nhận nhiệm vụ phân giải địa chỉ cho tất cả các thiết bị khác. Theo đó các máy trạm không cần giữ bảng định tuyến nữa Router C sẽ có nhiệm vụ thực hiện, trả lời tất cả các ARP request của tất cả các máy.

## 4. Các bản tin ARP và ARP Caching

### 4.1. Các bản tin ARP

* ARP probe: Đây là loại bản tin ARP dùng để máy thăm dò xem địa chỉ mà máy được cấp phát (cấu hình manual hoặc DHCP, ...) có bị trùng với địa chỉ IP của máy nào trong cùng mạng hay không. Khi mới ban đầu, các máy đều thực hiện broadcast bản tin ARP này.

    Bản tin này có cấu trúc địa chi IP của máy gửi là 0.0.0.0 (thể hiện máy gửi bản tin này chưa xác định IP, đồng thời cũng là để các máy khác trong mạng không cập nhật MAC của máy vào ARP caching - vì nó chưa được gán IP cụ thể nào)

    Địa chỉ MAC đích là 00:00:00:00:00:00

    Địa chỉ IP đích là địa chỉ IP mà máy gửi được cấp phát.

    Thông thường bản tin ARP request này sẽ không có reply.


* ARP announcements: ARP cũng sử dụng một cách đơn giản để thông báo tới các máy trong mạng khi địa chỉ IP hoặc địa chỉ MAC của nó thay đổi. Đó chính là bản tin gratuitous ARP

    Bản tin Gratuitous ARP được gửi broadcast request trong mạng với địa chỉ MAC và IP máy gửi là địa chỉ sau khi thay đổi.

    Địa chỉ MAC đích là 00.00.00.00.00.00. Địa chỉ IP đích là chính nó. Điều này đảm bảo các máy trong mạng khi nhận được bản tin này sẽ chỉ cập nhật địa chỉ MAC và IP của máy gửi vào trong ARP caching của mình => không có bản tin reply cho bản tin này.

    Hình minh họa: Bản tin Gratuitous ARP bắt được từ Wireshark.

* ARP request: Là bản tin ARP request mà máy gửi gửi broadcast để tìm địa chỉ MAC của máy nhận.

    Địa chỉ MAC và IP gửi là địa chỉ của máy gửi.

    Địa chỉ MAC nhận được set là 0 hết.

    Địa chỉ IP nhận là địa chỉ IP của máy cần tìm.


* ARP reply: Là bản tin mà máy nhận sau khi nhận được ARP request sẽ đóng gói lại MAC của mình và gửi bản tin reply về cho máy gửi.

    Nó sẽ đóng gói là địa chỉ IP và MAC của mình vào địa chỉ SHA và PHA.

    Địa chỉ mà máy gửi gửi tới nó sẽ được đóng gói và phần địa chỉ THA và TPA.

    Gửi bản tin unicast

### 4.2. ARP Caching

ARP là một giao thức phân giải địa chỉ động. Quá trình gửi gói tin Request và Reply sẽ tiêu tốn băng thông mạng. Chính vì vậy càng hạn chế tối đa việc gửi gói tin Request và Reply sẽ càng góp phần làm tăng khả năng họat động của mạng.

Từ đó sinh ra nhu cầu của ARP Caching.

Ngoài việc làm giảm lưu lượng mạng, ARP cache cũng đảm bảo độ phân giải các địa chỉ thường dùng là nhanh chóng, đảm bảo hiệu suất hoạt động tổng thể của mạng.

ARP Cache có dạng giống như một bảng tương ứng giữa địa chỉ hardware và địa chỉ IP.

(Trong Window: dùng câu lệnh arp -a trong Command Prompt để show ra ARP cache trong máy)

    Có hai cách đưa các thành phần tương ứng vào bảng ARP :

        Static ARP Cache Entries: Đây là cách mà các thành phần tương ứng trong bảng ARP được đưa vào lần lượt bởi người quản trị. Công việc được tiến hành một cách thủ công.

            Sử dụng trong trường hợp mà các workstation nên có static ARP entry đến router và file server nằm trong mạng. Điều này sẽ hạn chế việc gửi các gói tin để thực hiện quá trình phân giải địa chỉ.

            Sử dụng câu lệnh arp -s ip_addr mac_addrđể thêm một Static ARP entri vào ARP cache.

            Nhược điểm: ngoài hạn chế của việc phải nhập bằng tay, static cache còn thêm hạn chế nữa là khi địa chỉ IP của các thiết bị trong mạng thay đổi thì sẽ dẫn đến việc phải thay đổi ARP cache.

        Dynamic ARP Cache Entries: Đây là quá trình mà các thành phần địa chỉ hardware/IP được đưa vào ARP cache một cách hoàn toàn tự động bằng phần mềm sau khi đã hoàn tất quá trình phân giải địa chỉ.

            Chúng được lưu trong cache trong một khoảng thời gian và sau đó sẽ được xóa đi.

            Dynamic Cache được sử dụng rộng rãi hơn vì tất cả các quá trình diễn ra tự động và không cần đến sự tương tác của người quản trị.

    Trong môi trường mạng thực tế, có nhiều lý do tác động dẫn tới sự ảnh hưởng làm thay đổi các thông tin về việc ánh xạ IP và MAC nên các thông tin trong dynamic cache sẽ được tự động xóa sau một khoảng thời gian nhất định. Quá trình này được thực hiện một cách hoàn toàn tự động khi sử dụng ARP với khoảng thời gian thường là 10 hoặc 20 phút (hoặc lâu hơn tùy vào loại thiết bị mà mình sử dụng, phụ thuộc nhà cung cấp). Sau một khoảng thời gian nhất định được lưu trong cache , thông tin sẽ được xóa đi. Lần sử dụng sau, thông tin sẽ được update trở lại. (đây là lúc mà các bản tin ARP announcements phát huy tác dụng).
