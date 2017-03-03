# Thử nghiệm tính năng cảnh báo Group của Prometheus:
Mô hình: Prometheus-server monitor 2 targets:
- targets 1 - Hà Nội: MySQL replication
- targets 2 - Hồ Chí Minh: Service MYSQL.

- rules
```sh
ALERT MySQLReplicationIOThreadStatus
  IF mysql_slave_status_slave_io_running==0
  FOR 1m
  LABELS { severity = "warning" }
  ANNOTATIONS { summary = "IO thread stop", severity="warning"}

ALERT MySQLstatus
  IF mysql_up==0
  FOR 30s
  LABELS { severity = "warning" }
  ANNOTATIONS { summary = "Mysql Process Down" }                                             
```

#1. Trường hợp 1: Để trống trong cấu hình Group_by: `Group_by []`
- Hà Nội: Stop IO thread.
- Hồ Chí Minh: Stop SQL service.

=> 1 cảnh báo được gửi đi với nội dung là 2 service bị stop.

![](https://raw.githubusercontent.com/linhlt247/tmp/master/Screenshot%20-%2003032017%20-%2015%3A00%3A50.png)

![](https://github.com/linhlt247/tmp/blob/master/Screenshot%20-%2003032017%20-%2015:20:57.png?raw=true)

#2. Trường hợp 2: Cấu hình Group_by theo **alertname**: `Group_by['alertname']
- Hà Nội: Stop IO thread.
- Hồ Chí Minh: Stop SQL service.

=> 2 cảnh báo riêng rẻ được gửi đi.

Giải thích: Bởi vì ở đây cấu hình Group_by theo alertname mà trong rules em cấu hình mỗi server có rules name khác nhau => 2 cảnh báo với 
rules name khác nhau được gửi đi.

![](https://raw.githubusercontent.com/linhlt247/tmp/master/Screenshot%20-%2003032017%20-%2013%3A57%3A55.png)

![](https://raw.githubusercontent.com/linhlt247/tmp/master/Screenshot%20-%2003032017%20-%2015%3A21%3A04.png)

#3. Trường hợp 3: Cấu hình Group_by theo **severity**: `Group_by['severity']
- Hà Nội: Stop IO thread.
- Hồ Chí Minh: Stop SQL service.

=> 1 cảnh báo được gửi đi với nội dung là 2 server bị stop.

Giải thích: Bởi vì trong phần rules em cấu hình cảnh bảo 2 service đều có cùng mức cảnh báo là `severity = warning`: Do đó, nó gộp nhóm
những cảnh báo có cùng mức cảnh báo và gửi vào 1 thông báo.

![](https://raw.githubusercontent.com/linhlt247/tmp/master/Screenshot%20-%2003032017%20-%2014%3A08%3A05.png)


![](https://raw.githubusercontent.com/linhlt247/tmp/master/Screenshot%20-%2003032017%20-%2015%3A21%3A16.png)

#4. Trường hợp 4: Comment Group_by: `#Group_by`
- Hà Nội: Stop IO thread.
- Hồ Chí Minh: Stop SQL service.

=> 1 cảnh báo được gửi đi với nội dung là 2 server bị stop.
![](https://raw.githubusercontent.com/linhlt247/tmp/master/Screenshot%20-%2003032017%20-%2014%3A14%3A58.png)


# Kết Luận
- Khi không khai báo gì trong Group_by thì nó sẽ gom tất cả vào 1 thông báo.
- Khi Comment Group_by thì nó mặc định sẽ gom thông báo theo `alertname`: Đây là lý do vì sao hôm trước em thử nghiệm, dù có cấu hình hay comment thì
vẫn gom nhóm!
```sh
On 23 August 2016 at 18:48, Alvaro Gil <zeva...@gmail.com> wrote:
OH, how I can just not grouping at all?

No, there's always grouping. This is as you want to try and minimise the number of notifications you get, as hundreds/thousands of notifications for a common issue isn't very useful.

Brian
```
link: https://groups.google.com/forum/#!searchin/prometheus-developers/group_by|sort:relevance/prometheus-developers/RQHDfPnZoso/oLUT-_HEBQAJ


- Group_by theo `labels` trong alert rules chứ không phải trong file config.