# Monitoring

"DevOps là sự kết hợp của con người, quy trình, và sản phẩm để có thể phân phối liên tục giá trị cho người dùng cuôi"
Một trong những key của DevOps là đảm bảo rằng ứng dụng chạy ở production phải thật chính xác và hiệu quả. 
Để đánh giá sức khỏe của ứng dụng trong môi trường production, cần phải theo dõi nhiều loại logs, metrics được tạo ra từ servers, hosts, application. Việc theo dõi sức khỏe cho từng thành phần và toàn bộ ứng dụng trên môi trường cloud là một thách thức vô cùng lớn.

### Pattern
Giống như các pattern cho việc phát triển phần mềm, có nhiều pattern cho việc vận hành ứng dụng. 3 pattern hữu dụng cho việc maintain ứng dụng là logging, monitoring, và alerts.

### Khi nào sử dụng logging?
Cho dù có cẩn thận đến đâu, ứng dụng luôn luôn có thể gặp những sự cố bất ngờ trong môi trường production. Khi người dùng phản ánh lỗi của ứng dụng, sẽ rất hữu ích để xem điều gì đã xảy ra với ứng dụng khi sự cố xảy ra. Việc ứng dụng ghi lại những gì nó đã làm gọi là logging. Mỗi khi có lỗi, sự cố xảy ra trên môi trường production, mục tiêu là tái tạo lại các điều kiện trên môi trường non-production. Việc có những bản ghi log tốt sẽ giúp lập trình viên có thể tái tạo lại những gì đã xảy ra trên môi trường production, từ đó có thể kiểm nghiệm, test và debug ứng dụng.

### Thách thức khi ghi log đối với ứng dụng cloud-native
Trong các ứng dụng truyền thống, log files thường được lưu trữ ở local machine. Trong thực tế, ở các hệ điều hành Unix, một thư mục được cấu trúc để lưu trữ bất kì các loại log, thường là /var/log. 
Những hữu ích của việc ghi log vào một file trên máy chủ bị giảm đi rất nhiều trên môi trường cloud. Các ứng dụng tạo ra logs có thể không có quyền truy cập vào local disk hoặc local disk có thể thay đổi liên tục đối với ứng dụng dạng container. Ngay cả việc scale ứng dụng dạng monolithic trên nhiều node cũng gây khó khăn cho việc xác định vị trí các tập tin log 
Ứng dụng cloud-native sử dụng kiến trúc microservices cũng gây ra nhiều thách thức cho file-based logger. Requests của người dùng có thể trải rộng ở nhiều services run ở các server khác nhau và có thể kèm theo serverless functions không có access đến local file system. Điều đó gây rất nhiều thách thức cho việc thu thập và tập hợp log của một user/session trên nhiều services/machines.
Cuối cùng, số lượng người dùng trên một vài ứng dụng cloud-native sẽ rất cao. Tưởng tượng mỗi user tạo ra hàng trăm dòng log khi đăng nhập vào ứng dụng. Đối với lượng người dùng lên tới hàng trăm ngàn và dung lượng logs đủ lớn để việc sử dụng các tools là cần thiết để truy vấn hiệu quả lượng logs đó.

### Logging in cloud-native applications
với các thách thức của file-based logs ở các ứng dụng cloud-native apps, log tập trung (centralized logs) được ưa thích hơn. Logs được thu thập bởi ứng dụng và chuyển đến ứng dụng log tập trung sau đó được đánh index và lưu trữ. Loại hệ thống này có thể xử lý hàng chục gigabyte logs mỗi ngày.
Một số practice tiêu chuẩn cho việc ghi logs giữa nhiều service là tạo ra ID khi bắt đàu một tương tác và ghi log nó với các tương tác liên quan. Điều này giúp dễ dàng hơn cho việc tìm kiếm.
Một ví dụ khác là đảm bảo rằng định dạng log giống nhau cho mọi dịch vụ, bất kể ngôn ngữ hoặc thư viện ghi logs nào được sử dụng. Việc tuân thủ một định dạng cho logs giúp cho việc tìm kiếm cũng dễ dàng hơn nhiều.

### Hình 7-4

Thách thức với việc phát hiện và phản ứng trước các tiềm ẩn rủi ro sức khỏe của ứng dụng.
Một vài ứng dụng không làm những nhiệm vụ quan trọng, có thể nó chỉ phục vụ nội bọ và khi nó gặp sự cố, người dùng có thể liên hệ với đội chịu trách nhiệm và ứng dụng có thể được khởi động lại.Tuy nhiên, khách hàng thường có kì vọng cao hơn cho ứng dụng họ đang sử dụng. Bạn nên là người biết trước những vấn đề của ứng dụng so với người dùng, hoặc trước khi người dùng thông báo đến bạn. Mặt khác, điều đầu tiên bạn biết về vấn đề là cơn giận dữ của người dùng trên mạng xã hội về ứng dụng của bạn hoặc thậm chí là cả tổ chức.
Một số kịch bản thường gặp:
 - Một service trong ứng dụng liên tục bị lỗi và khởi động lại dẫn đến slow response.
 - Trong một vài thời điểm của ngày, response time của ứng dụng rất chậm.
 - Sau vài lần triển khai, tải trên database tăng gấp nhiều lần.
Monitoring có thể cho bạn biết về các điều kiện dẫn đến các rắc rối, cho phép bạn giải quyết các điều kiện cơ bản trước khi chúng dẫn đến bất kỳ tác động đáng kể nào đến người dùng.


### Monitoring cloud-native apps

Một số hệ thống giám sát tập trung đảm nhận vài vai trò bổ sung ngoài việc thu thập log thuần túy. Nó có thể thu thập các số liệu (metrics), như thời gian query database, thời gian phản hồi trung bình của web server, hay tải CPU, memory của hệ điều hành. Kết hợp với logs, các hệ thống này có thể cung cấp một cái nhìn toàn diện về sức khỏe của mỗi node trong hệ thống và toàn bộ ứng dụng.

Các hệ thống giám sát cũng xây dựng các query, truy vấn để tổng hợp logs, hoặc thống kê các dữ liệu, sau đó hiển thị thông tin ở dạng đồ họa, biểu đồ trên các dashboard. Thông thường, các đội giám sát thường đặt các màn hình lớn để hiển thị thông tin thu thập được. Bằng cách này, sẽ dễ dàng theo dõi được các vấn đề xảy tới.

Hệ thống giám sát cloud-native cung cấp khả năng theo dõi từ xa theo thời gian thực và khoét sâu vào ứng dụng ngay cả khi ứng dụng là nguyên khối monolithic hay microservices phân tán. Hệ thống giám sát bao gồm các công cụ giúp thu thập thông tin ứng dụng, query, và hiển thị thông tin về sức khỏe ứng dụng.

### Challenges with reacting to critical problems in cloud-native apps

Khi bạn cần phải phản ứng với các vấn đề xảy ra với ứng dụng của mình, bằng cách nào đó bạn phải thông báo cho người cần được thông báo. Pattern này depends on logging và monitoring. Dữ liệu metrics, health cần được thu thập lại một chỗ, cùng với đó là tạo ra các rule để trigger alert khi các metrics vượt qua ngưỡng chấp nhận được.
Alert là lớp phía trên của monitoring. Khi một điều kiện nhất định được kích hoạt, thông báo sẽ được gửi đến các thành viên của dự án về các vấn đề khẩn cấp. Một số tình huống cụ thể:
 - Một trong số các services không phản hồi sau 1 phút downtime.
 - Ứng dụng trả về HTTPcode không thành công với kpi > 1% lượng request.
 - Avg response time vượt ngưỡng > 2000ms

### Alerts in cloud-native apps.

Bạn có thể tạo ra các query trên các hệ thống giám sát để truy vấn các failure conditions, cấu hình cảnh báo bằng email hoặc sms cho đội dự án khi conditions được trigger để truy tìm nguyên nhân.
Typically, một 500 error response chưa đủ để determine problem. Nó có thể là lỗi của người dùng khi điền thông tin không hợp lê. -> Alerts query cần được tạo ra với các query đối với các giá trị trung bình.

Một vấn đề của alert là gửi quá nhiều cảnh báo cho người dùng. Đội dự án sẽ nhanh chóng giảm mẫn cảm đối với các cảnh báo. Sau đó, khi một lỗi thực sự diễn ra, họ có thể bỏ xót giữa hàng trăm cảnh báo sai. Việc đảm bảo cảnh báo kích hoạt đối với một sự cố chính xác là vô cùng quan trọng!

### Logging with Elastic Stack

Có rất nhiều hệ thống giám sát tập trung và chúng bao gồm cả free, open-source, hay trả phí. Trong nhiều trường hợp, free tools tốt tương đương hoặc thậm chí tốt hơn với các tools trả phí. Một hệ thống giám sát combine 3 open-source components: Elastic search, Logstash, và Kibana.
Hệ thống này được gọi ngắn gọn dưới cái tên Elastic stack hoặc ELK stack

### Elastic stack
Elastic stack là công cụ  mạnh mẽ để thu thập thông tin từ kubernetes cluster. Kubernetes hỗ trợ gửi log tới Elasticsearch endpoint.

### Advantages of Elastic Stack?
- Elastic Stack cung cấp một giải pháp giám sát tập trung low-cost, scalable, cloud-friendly. 
- Giao diện thân thiện
- Tiếp nhận được nhiều loại đầu vào.
- Tìm kiếm nhanh với dữ liệu lớn. -> Giúp các ứng dụng có dữ liệu lớn vẫn có thể ghi log chi tiết mà tìm kiếm hiệu quả.

### Logstash.

Thành phần đầu tiên của stack là Logstash. Công cụ này được sử dụng để thu thập thông tin log từ nhiều nguồn dữ liệu. Logstash có thể đọc logs từ ổ đĩa và có thể tiếp nhận thông tin từ nhiều logging library . Logstash có thể làm một số filter khi tiếp nhận logs. Ví dụ, khi trong log chứa thông tin về IP, Logstash có thể được cấu hình để tạo ra biểu đồ geographical cho nguồn log.
Có một công cụ khác có thể thay thế logstash là Beats, một số ứng dụng sử dụng cả beats và logstash.
Khi logs đã được thu thập, chúng cần một nơi nào đó thể chứa lại. Trong khi Logstash support nhiều output, một trong số đó là Elastic search.

### Elastic search.

Elastic search là một công cụ tìm kiếm mạnh mẽ, nó đánh index dữ liệu log ngay khi tiếp nhận. Điều này giúp việc query giữa log rất nhanh. Elastic search có thể handle huge quantities of logs and, in extreme cases, can be scaled out across many nodes.

### Visualizing information with Kibana web dashboards.

Thành phần cuối cùng của stack là Kibana. Công cụ này cung cấp interactive visualizations in a web dashboard. Các dashboard có thể tạo ra với những người không phải là dân kĩ thuật. Hầu hết các dữ liệu được đánh index trong Elasticsearch đều có trên Kibana dashboards. Mỗi người dùng có thể mong muốn các dashboard khác nhau và Kibana cho phép tùy chỉnh thông qua dashboard của riêng người dung.

# DEVOPS

Cloud-native applications are not immune to that same dichotomy. They have clear advantages in tearms of speed of development, stability, and scalability, but managing them can be quite a bit more difficult.
Years ago, không khó để thấy quá trình đưa ứng dụng từ môi trường phát triển đến môi trường production mất cả tháng, và thậm chí nhiều hơn. 

### Repository per microservice

- Instructions for building and maintaining the application can be added to a README file at the
root of each repository. When flipping through the repositories, it’s easy to find these
instructions, reducing spin-up time for developers.
- Every service is located in a logical place, easily found by knowing the name of the service.
- Builds can easily be set up such that they’re only triggered when a change is made to the
owning repository.
- The number of changes coming into a repository is limited to the small number of developers
working on the project.
- Security is easy to set up by restricting the repositories to which developers have read and write
permissions. 
- Repository level settings can be changed by the owning team with a minimum of discussion with
others. 
### Single repository
