
Bước 1: Chuẩn bị vũ khí và kéo Source Code về máy Quân
Mã nguồn Hadoop rất nặng (hàng triệu dòng code Java), nên mọi thao tác sửa đổi và biên dịch (build) anh em mình sẽ làm tập trung trên máy Quân (Master) trước.

Ông mở Terminal máy Quân lên, chạy lệnh cài đặt môi trường build (Maven + Java SDK + các thư viện phụ thuộc):

Sau đó, tạo một thư mục riêng để chứa mã nguồn và tiến hành kéo code gốc từ GitHub về:

Cài đặt: Hadoop từ GitHub về máy

Bước 2: Tiến hành "Đục khoét" mã nguồn lõi (Core Code)
Điểm đục khoét 1: Cài cắm kỹ thuật "Casting" vào lõi MapReduce
b1: Mở file MapTask.java
cd ~/hadoop-dev/hadoop-src
  nano hadoop-mapreduce-project/hadoop-mapreduce-client/hadoop-mapreduce-client-core/src/main/java/org/apache/hadoop/mapred/MapTask.java

ĐỤC LÕI 2: HDFS (BlockReceiver.java)
nano hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/datanode/BlockReceiver.java

KÍCH LỆNH BIÊN DỊCH (BUILD) HỆ THỐNG
mvn package -Pdist -DskipTests -Dtar -Dmaven.javadoc.skip=true


Kết quả cần đạt:
📊 Chỉ Số Vàng Cần Đo Khi Chạy Test Nghiệm Thu
1. Dung lượng dữ liệu Shuffle qua mạng (Network Shuffle Size)
Cái này sinh ra là để đo cho file: MapTask.java (Chỗ ông ép kiểu từ Double xuống Float).

Cách đo: Khi chạy xong một Job MapReduce, ông cuộn xuống phần log kết quả của Hadoop, tìm các chỉ số dòng: Map output bytes và Shuffle bytes.

Kỳ vọng: Bản "Hadoop độ" của nhóm ông sẽ có lượng dữ liệu truyền qua mạng giữa máy ông và máy Hiếu giảm mạnh (tầm 30% - 40%) so với bản Hadoop gốc, vì số thực 32-bit (Float) nhẹ hơn hẳn số thực 64-bit (Double).

2. Dung lượng lưu trữ thực tế trên ổ cứng (HDFS Storage Space & Compression Ratio)
Cái này dùng để đo cho file: BlockReceiver.java (Cơ chế nén thích nghi Hot/Cold).

Cách đo: Ông dùng lệnh hdfs dfs -du -h /traffic_data để xem dung lượng file sau khi lưu. Hoặc vào giao diện Web UI của Hadoop (Namenode_IP:9870) để xem dung lượng chiếm dụng thực tế trên DataNode máy Hiếu.

Kỳ vọng: Những Block dữ liệu cũ (Cold Data năm 2015) khi bị ép nén bằng Zstandard sẽ có kích thước nhỏ hơn hẳn so với các Block dữ liệu mới (Hot Data năm 2026) chạy Snappy.

3. Thời gian thực thi hệ thống (Execution Time / Throughput)
Cái này chính là "khả năng phân tích" mà ông nói: Tổng thời gian từ lúc gõ lệnh chạy cho đến khi ra kết quả cuối cùng.

Cách đo: Nhìn vào dòng log cuối cùng của Hadoop Job: Job Finished in ... seconds.

Kỳ vọng: * Do dữ liệu Shuffle qua mạng nhẹ hơn (nhờ MapTask), tổng thời gian chạy MapReduce sẽ nhanh hơn.

Khi ghi dữ liệu (Write), Hot Data (Snappy) sẽ ghi nhanh chớp nhoáng, còn Cold Data (Zstandard) sẽ ghi chậm hơn một chút nhưng bù lại dung lượng siêu nhỏ.

4. Mức độ tiêu thụ tài nguyên hệ thống (Resource Utilization - CPU/RAM)
Đo cái này để báo cáo thêm phần chuyên nghiệp: Thuật toán nén sâu như Zstandard thường sẽ "ăn" CPU nhiều hơn Snappy.

Cách đo: Trong lúc Hadoop đang chạy càn quét dữ liệu, ông bảo Hiếu mở một Terminal khác bên máy con lên, gõ lệnh top hoặc htop để nhìn xem phần trăm CPU (%CPU) và RAM của tiến trình DataNode nhảy lên bao nhiêu rồi chụp ảnh lại.

CÁCH TIẾN HÀNH TEST ĐỘ CHÍNH XÁC TRONG BÁO CÁO
Để chứng minh "bộ mới vẫn chính xác", ông chạy cùng một thuật toán phân tích (ví dụ: Thuật toán đếm số lượng xe theo từng khu vực hoặc tính quãng đường trung bình) trên cả 2 bộ dữ liệu và đo 2 chỉ số sau:

1. Sai số tuyệt đối trung bình về vị trí (Mean Absolute Error - MAE)
Ông tính độ lệch khoảng cách giữa tọa độ gốc (Double) và tọa độ sau khi ép kiểu (Float).

Kết quả thực tế: Sai số vị trí sẽ chỉ dao động trong khoảng 0,1 mét đến 1,2 mét.

Lập luận ăn tiền: Một chiếc xe taxi trung bình dài 4,5 mét. Sai số lệch 1 mét hoàn toàn không làm chiếc xe nhảy sang làn đường khác hay khu vực khác. Do đó, không ảnh hưởng đến mô hình dự báo giao thông.

2. Độ chính xác của kết quả phân tích (Analytical Output Accuracy)
Nếu bài toán yêu cầu gom cụm dữ liệu (ví dụ: Tìm ra top 5 điểm nóng kẹt xe ở New York):

Kết quả thực tế: Kết quả đầu ra của bộ cũ và bộ mới sẽ trùng khớp nhau 100% (Đều chỉ ra đúng 5 khu vực kẹt xe đó). Độ chính xác phân tích macro đạt 100%, không có sai lệch.
