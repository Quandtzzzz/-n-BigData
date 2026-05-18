🗺️ LỘ TRÌNH 4 GIAI ĐOẠN TRIỂN KHAI ĐỀ TÀI
Giai đoạn 1: Xây dựng lõi Hadoop Cluster (Hạ tầng lưu trữ & xử lý)
- Cài đặt Java (JDK 8) trên cả 2 máy.

- Tải và giải nén source Hadoop.

- Cấu hình các file lõi: core-site.xml, hdfs-site.xml, yarn-site.xml, mapred-site.xml.

- Định nghĩa máy nào làm Master (NameNode/ResourceManager) và máy nào làm Worker (DataNode/NodeManager).

- Format NameNode và khởi động cụm (Start-dfs, Start-yarn).

Giai đoạn 2: Triển khai Stream Processing (Luồng dữ liệu thời gian thực)
Vì anh em đang có hứng thú với việc xử lý dữ liệu luồng (stream), giai đoạn này sẽ dựng Apache Kafka và Zookeeper lên Cluster.

- Cấu hình Zookeeper để quản lý các node.

- Tạo các Kafka Topic để làm ống dẫn dữ liệu truyền vào.

Giai đoạn 3: Code Ứng dụng & Tích hợp (Bằng Python/Java)

- Viết Producer: Thu thập dữ liệu đầu vào (text, log, hoặc tin nhắn) đẩy vào Kafka.

- Viết Stream Processor / Consumer: Xử lý dữ liệu ngay khi nó vừa chạy tới (ví dụ: bóc tách từ khóa, lọc dữ liệu) và lưu kết quả xuống HDFS (Hadoop Distributed File System).

- Sử dụng MapReduce (hoặc Spark nếu anh em muốn nâng cao) để lôi dữ liệu từ HDFS ra thống kê, phân tích.

Giai đoạn 4: Đóng gói & Demo
- Chạy thử toàn bộ luồng: Từ lúc đẩy dữ liệu vào Worker $\rightarrow$ Master nhận và phân phối $\rightarrow$ Ghi xuống HDFS.

- Lấy log hệ thống và giao diện Web UI của Hadoop (cổng 9870, 8088) để minh chứng phân tán thành công đưa vào báo cáo.
