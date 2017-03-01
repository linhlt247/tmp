Phần cảnh báo em làm lại và có kết quả như sau anh ạ: 
- Vấn đề gửi cùng lúc đến nhiều nơi: OK.  Em cấu hình gửi đồng thời đến 2 địa chỉ email, channel slack.

- Sau khi đẩy cảnh báo,  có thể tiếp tục đẩy cảnh báo đến người khác khác sau? Phần này thằng Alert manager 
có tính năng `repeat`, thông qua dòng cấu hình `repeat_interval`: Tính năng này cho phép gửi lại các cảnh báo đã gửi.

- Phân mức cảnh báo, gửi đến các đối tượng khác nhau? Cái này cũng OK luôn.
Tuy nhiên, việc phân mức cảnh báo sẽ là do mình tự đặt theo nhãn chứ không có sẵn các mức cảnh báo.
Ví dụ như với cảnh báo A, thì có nhãn là `warning`, cảnh báo B sẽ có nhãn là `critical`.
Thì trong phần cấu hình `route` đường đi của cảnh báo, mình sẽ dùng tùy chọn `match`, kiểu như là lọc ra các labels nào sẽ đi đường nào.
Nó cũng hỗ trợ match bằng cách dùng `regular expression`.

- Tính năng Inhibition: Có nghĩa là khi 1 cảnh báo được gửi đi, thì các cảnh báo phụ khác không cần phải gửi đi nữa.
```sh
inhibit_rules:
- source_match:
    severity: 'critical'
  target_match:
    severity: 'warning'
```
Ví dụ: Khi cảnh báo có nhãn `critical` được gửi đi, thì các cảnh báo `warning` không cần phải gửi đi nữa.


- Tính năng Grouping. Theo như em đọc ở documents có đoạn này nó lấy ví dụ như này: 
```sh
Example: Dozens or hundreds of instances of a service are running in your cluster when a network partition occurs.
Half of your service instances can no longer reach the database.
Alerting rules in Prometheus were configured to send an alert for each service instance if it cannot communicate with the database.
As a result hundreds of alerts are sent to Alertmanager.
```
Thì theo em hiểu là thay vì gửi 100 email với nội dung của từng instance thì nó sẽ gửi 1 email, và 1 email này sẽ có nội dung
của 100 instance kia. Em nghĩ như vậy có đúng ko ạ?

Em đã thử nghiệm khi cấu hình group by theo `alertname`, tức là nó sẽ gom các thông báo có cùng `alertname`, với 2 máy chủ.
Khi em cho down cả 2 máy chủ, kết quả nó gửi 1 cảnh báo với nội dung là 2 máy down.
Tuy nhiên, khi em bỏ đi phần cấu hình `group by` thì kết quả vẫn như trên????
