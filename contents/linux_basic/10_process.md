# Process

Một process là một thể hiện của một hay nhiều task


## pstree 

Lệnh hiển thị process chạy trong hệ thống theo kiểu cây sơ đồ biểu hiện mối liên hệ giữa một process với 
process cha của nó và vs tiến trình khác do nó tạo ra, threads biểu hiện trong dấu {}

```
toan@toan:~$ pstree
init─┬─acpid
     ├─atd
     ├─cron
     ├─dbus-daemon
     ├─dhclient
     ├─dnsmasq
     ├─5*[getty]
     ├─irqbalance
     ├─libvirtd───10*[{libvirtd}]
     ├─login───bash
     ├─rpc.idmapd
     ├─rpc.statd
     ├─rpcbind
     ├─rsyslogd───3*[{rsyslogd}]
     ├─sshd─┬─sshd───sshd───bash─┬─pstree
     │      │                    ├─vi
     │      │                    └─2*[wc]
     │      └─sshd───sshd───sftp-server
     ├─systemd-logind
     ├─systemd-udevd
     ├─upstart-file-br
     ├─upstart-socket-
     └─upstart-udev-br
```
	 
Để tắt process ta dùng lệnh `kill -SIGKILL <pid> hoặc kill -9 <pid>. Chú ý lệnh chỉ kill được process thuộc sở hữu của user 
còn của user khác thì không được trừ khi ta phải là root.

## Lệnh top

Lệnh này cho ta rất nhiều thông tin cả về phần cứng và tiến trình

```
toan@toan:~$ top
top - 23:26:33 up  1:38,  2 users,  load average: 0.00, 0.00, 0.00
Tasks: 180 total,   1 running, 176 sleeping,   3 stopped,   0 zombie
%Cpu(s):  0.2 us,  0.0 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem:   2030820 total,   266436 used,  1764384 free,    32064 buffers
KiB Swap:  2094076 total,        0 used,  2094076 free.   113236 cached Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
  1588 toan      20   0  103380   4940   3740 S   0.3  0.2   0:00.41 sshd
     1 root      20   0   33604   4160   2740 S   0.0  0.2   0:02.25 init
     2 root      20   0       0      0      0 S   0.0  0.0   0:00.01 kthreadd
     3 root      20   0       0      0      0 S   0.0  0.0   0:00.03 ksoftirqd/0
     5 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H
     7 root      20   0       0      0      0 S   0.0  0.0   0:00.41 rcu_sched
     8 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcu_bh
     9 root      rt   0       0      0      0 S   0.0  0.0   0:00.00 migration/0
    10 root      rt   0       0      0      0 S   0.0  0.0   0:00.02 watchdog/0
    11 root      rt   0       0      0      0 S   0.0  0.0   0:00.02 watchdog/1
    12 root      rt   0       0      0      0 S   0.0  0.0   0:00.00 migration/1
    13 root      20   0       0      0      0 S   0.0  0.0   0:00.02 ksoftirqd/1
    14 root      20   0       0      0      0 S   0.0  0.0   0:00.00 kworker/1:0
    15 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/1:0H
    16 root      20   0       0      0      0 S   0.0  0.0   0:00.00 kdevtmpfs
    17 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 netns
    18 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 perf
```

* Dòng đầu tiên cho biết thông tin về thời gian hệ thống up (1:38) số users đã log on, tải trung bình của CPU trong 1 phút, 5 phút, 15 phút

* Giá trị load average chỉ ra hệ thống có bận không? Nếu load average là 1.00/Cpu nghĩa là đang hoạt động hết khả năng nhưng hệ thống chưa overloaded. Nếu
giá trị này >1 tức là các process đang trinh giành CPU time. Nếu load average rất cao thì hệ thống có vấn đề, có thể rơi vào trạng thái treo.

* Dòng 2 của top chỉ ra tổng số process, số đang running, đang sleeping, đang stopped và zombie process. Ý nghĩa dòng này gần giống tải trung bình
nó chỉ ra cụ thể hơn cho load average.

* Dòng 3 chỉ ra cách thời gian CPU được chia ra giữa các users(us) và kernel(sy). Idle(id) rất cao nếu load average thấp và nguowic lại, các job đang chờ
để I/O lắng nghe. để chỉ % hardware hoặc sofware bị interupts dùng hi và si, giá trị st dùng cho VM ddeeer xem số idle cpu time dành cho các user khác.

* Dòng 4 và 5 chỉ ra các giá trị về Mem và swap, cần để ý đến giá trị buffers và cached 

* Các dòng về proces gồm các thành phần

** PID
** Priority(PR) và nice values (NI)
** Process owner (USER) 
** Virtual (VIRT), physical (RES), shared memmory (SHR)
** % CPU, %MEM đã dùng
** Excute time (TIME+)
** Command (COMMAND)

## Background và foreground proces

Foreground proces là tiến trình chạy ngay trên shell khi nó đang chạy thì không thể làm gì khác với shell đó ngoài việc chờ cho nó chạy hết hoặc kill
nó giữa chừng. Còn background process chạy ẩn, ta có thể thao tác với các scripts khác. Để chạy background proces ta thêm dấu & vào cuỗi lệnh 

## Scheduling process

Đây là cách để hẹn giờ cho process tự bật lên mà không cần gõ lệnh  bằng tay nữa.

Có nhiều tool để thực hiện điều này như atq hay at. Tuy nhiên thông dụng nhất là đặt lịch bằng crontab. 
File cấu hình /etc/crontab chứa những lệnh khác nhau để lập lịch. Mỗi dòng của crontab ứng với 1 việc, có lệnh thực hiện ứng với express thời gian
Để mở file crontab ta dùng lệnh `crontab -e` hoặc export EDITOR=vi rồi vi crontab






