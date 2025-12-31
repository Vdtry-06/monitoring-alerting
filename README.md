# monitoring-alerting

## 1. Các khái niệm cốt lõi
```
1. Metric: Các thông số giám sát (CPU usage, RAM usage, Error rate…)
2. Time Series Database (TSDB): Loại cơ sở dữ liệu chuyên dụng để lưu trữ Metric theo chuỗi thời gian (mỗi giá trị đi kèm một mốc thời gian cụ thể). TSDB giúp truy xuất dữ liệu lớp cực nhanh
3. Threshold (ngưỡng) : Giá trị giới hạn được thiết lập. Nếu Metric vượt ngưỡng này (ví dụ CPU > 80%), hệ thống sẽ kích hoạt cảnh báo

```
## 2. Tech stack: Prometheus & Grafana
* Exporter: Các agent cài trên đối tượng cần giám sát (server, database, app) để thu thập dữ liệu thô và đẩy cho phép Prometheus lấy dữ liệu
* Prometheus Server: "Bộ não" của hệ thống, Thực hiện lấy dữ liệu Exporter (Pull mechanism), lưu trữ và xử lý truy vấn
* PromQL: Ngôn ngữ truy vấn của Prometheus để tính toán các thông số phức tạp  
* Alert Manager: Tiếp nhận các vi phạm ngưỡng từ Prometheus, phân loại và gửi thông báo đến các kênh như Telegram, Email, Slack,...
* Grafana: Công cụ trực quan hóa dữ liệu (Visualize). Grafana kết nối vào Prometheus để vẽ nên các biểu đồ, dashboard đẹp mặt và dễ hiểu cho người vận hành.


## 3. Hướng dẫn thiết lập hệ thống cảnh báo
```
3.1. Chạy docker: docker-compose up -d
3.2. Xem logs: docker logs name_container
3.3. URL:
    - alertmanager: http://localhost:9093
    - node exporter: http://localhost:9100/metrics
    - prometheus: http://localhost:9090
    - grafana: http://localhost:3001 (admin/admin)
```
### 3.4. Setup Grafana:
#### 3.4.1. Thêm Prometheus làm Data Source
Bước 1: Vào menu bên trái
* Click vào ☰ (hamburger menu)
* Chọn Connections → Data sources

Bước 2: Add Prometheus
* Click "Add new data source"
* Chọn Prometheus
* Cấu hình:

        Name: Prometheus
        URL: http://prometheus:9090
        Để các option khác mặc định
        Click "Save & Test" ở cuối trang
        Phải thấy thông báo "Data source is working"

#### 3.4.2. Thêm Dashboard mới
Cách 1: Thủ công

Cách 2: Import Dashboard có sẵn: 
* Bước 1: Import Dashboard:

        Click ☰ → Dashboards → New → Import
* Bước 2: Nhập Dashboard ID
        Nhập một trong các ID sau (từ grafana.com):
        Node Exporter Full Dashboard:

        Dashboard ID: 1860
        Click "Load"

        Hoặc các dashboard phổ biến khác:

        11074 - Node Exporter for Prometheus Dashboard
        12486 - Node Exporter Full with Alerts
        13978 - Node Exporter Quickstart

* Bước 3: Cấu hình Import

        Chọn Prometheus data source
        Click "Import"

#### 3.5. Query trên Prometheus:

```
1. CPU Usage %:
100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

2. Memory Usage %
(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100

3. Disk Usage %
100 - ((node_filesystem_avail_bytes / node_filesystem_size_bytes) * 100)

4. Network Traffic Rate
rate(node_network_transmit_bytes_total[5m])

5 Disk IOPS
rate(node_disk_reads_completed_total[5m]) + rate(node_disk_writes_completed_total[5m])
```

#### 3.6. Minh chứng cảnh báo:
![alt text](image.png)
