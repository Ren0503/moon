---
layout: post
title: Kiến Trúc PostgreSQL
date:   2021-10-14
excerpt: Hiểu về kiến trúc của postgresql.
tags: [database, postgresql]
comments: true
---

Ở hiện tại PostgreSQL có lẽ là cơ sở dữ liệu tiên tiến nhất trong trong các RDBSM trên thị trường. Được giới thiệu lần đầu vào năm 1989, cho đến nay đã có rất nhiều cải tiến. Theo [db_engines](https://db-engines.com/en/ranking), nó là cơ sở dữ liệu phổ biến thứ 4 trên thế giới (tại thời điểm viết bài).

# Kiến trúc PostgreSQL

Cấu trúc vật lý của PostgreSQL tương đối đơn giản. Nó bao gồm một bộ nhớ chung, một vài tiến trình nền và các file dữ liệu.

![structure](/assets/img/postgresql/structure.jpg)

## Bộ nhớ chung

Bộ nhớ chung là bộ nhớ dành riêng cho lưu trữ cho bộ nhớ đếm cơ sở dữ liệu và nhật ký giao dịch. Hai thành phần quan trọng của bộ nhớ chung là Shared Buffer và WAL Buffer.

## Shared Buffer

Mục đích của Shared Buffer là để giảm thiểu các tác vụ I/O lên đĩa (DISK IO). Để đạt được mục đích đó, phải đáp ứng được những nguyên tắc sau:
- Ta cần truy cập bộ nhớ đệm lớn(hàng chục, trăm gigabites) nhanh chóng.
- Tối thiểu hoá xung đột khi nhiều người dùng truy cập cùng lúc.
- Các blocks được sử dụng thường xuyên phải ở trong bộ đệm càng lâu càng tốt.

## WAL Buffer

WAL Buffer là bộ nhớ đệm để lưu trữ tạm thời các thay đổi của cơ sở dữ liệu. Nội dung lưu trữ trong WAL Buffer sẽ được ghi lại vào file WAL tại một thời điểm đã xác định trước.Vì các vấn đề về sao lưu và phục hồi dữ liệu, nên WAL Buffer và file WAL là rất cần thiết.

# Loại tiến trình PostgreSQL

PostgreSQL có bốn loại tiến trình là:

1. Postmaster (Daemon) Process
2. Background Process
3. Backend Process
4. Client Process

## Postmaster Process

Postmaster Process là tiến trình khởi tạo đầu tiên sau khi PostgreSQL khởi động. Tiến trình này đảm nhiệm việc khởi động hoặc dừng các tiến trình khác nếu cần hoặc có yêu cầu. Sau khi khởi động postmaster process sẽ khởi động các background process.

![diagram](/assets/img/postgresql/diagram.jpg)

Nếu bạn kiểm tra mối quan hệ giữa các tiến trình bằng `pstree`, bạn sẽ thấy Postmaster Process là tiến trình cha của tất cả các tiến trình(để rõ ràng, tôi đã thêm tên và đối số theo sau id của tiến trình).

![cmd_pstree](/assets/img/postgresql/cmd.png)

## Background Process

Ta có một bảng các Background Process cần có trong PostgreSQL như sau:
Ta có danh sách các Background Process thường trực như sau:

### logger 

```bash
postgres 17400 17397  0 Apr11 ?        00:00:18 postgres: logger process    
```

Tiến trình này đảm nhiệm việc ghi log của PostgreSQL.

### checkpointer 

```bash
postgres 17400 17397  0 Apr11 ?        00:00:18 postgres: checkpointer process    
```

Tiến trình này chủ yếu giữ vai trò thực hiện checkpoint (đồng bộ dữ liệu từ bộ nhớ đệm xuống vùng lưu trữ) khi cần thiết.

### writer 

```bash
postgres 17401 17397  0 Apr11 ?        00:01:24 postgres: writer process     
```

Tiến trình này kết hợp với checkpointer để đảm bảo việc ghi dữ liệu từ bộ đệm xuống vùng lưu trữ. Thông thường khi checkpoint không hoạt động, tiến trình này sẽ ghi từng chút một dữ liệu xuống vùng lưu trữ.

### wal writer

```bash
postgres 17402 17397  0 Apr11 ?        00:01:31 postgres: wal writer process    
```

Đảm nhiệm việc đồng bộ WAL từ bộ nhớ đệm xuống vùng lưu trữ. Thông thường WAL sẽ được ghi từ bộ đệm xuống vùng lưu trữ khi transaction được commit. Nếu dữ liệu đệm của WAL trên bộ nhớ đệm vượt quá tham số `wal_buffers`, dữ liệu WAL trên vùng nhớ đệm sẽ tự động ghi xuống vùng lưu trữ dữ liệu thông qua tiến trình này.

### autovacuum launcher 

```bash
postgres 17403 17397  0 Apr11 ?        00:06:13 postgres: autovacuum launcher process    
```

Tiến trình này hoạt động khi tham số `autovacuum = on`. Nó thực hiện chức năng tự động lấy vùng dữ liệu dư thừa sau khi DELETE hoặc UPDATE dữ liệu. Tiến trình này khởi động các VACUUM worker processes sau mỗi `autovacuum_naptime`. Các VACUUM worker processes sẽ thực hiện việc [VACUUM](https://www.postgresql.vn/blog/vacuum) dữ liệu trên các database.

### stats collector

```bash
postgres 17404 17397  0 Apr11 ?        00:11:30 postgres: stats collector process    
```

Tiến trình này thực hiện vai trò lưu trữ các thông tin thống kê hoạt động của PostgreSQL và cập nhật vào các system catalog (thông tin nội bộ của PostgreSQL hiện diện bởi các bảng `pg_stat_all_tables` hoặc `pg_stat_activity`).

## Backend Process

Số lượng tối đa backend process được thiết lập bởi tham số max_connections có giá trị mặc định là 100. Backend process thực hiện yêu cầu truy vấn của user process, sau đó truyền kết quả. Một số cấu trúc bộ nhớ được yêu cầu để thực thi truy vấn, được gọi là bộ nhớ cục bộ (local memory). Các tham số chính liên quan đến bộ nhớ cục bộ là:
1. Không gian `work_mem` được sử dụng để sắp xếp, tính toán bit, hash joins và merge joins. Thiết lập mặc định là 4 MB.
2. Không gian `Maintenance_work_mem` được sử dụng cho VACUUM và CREATE INDEX. Thiệt lập mặc định là 64 MB.
3  Không gian `Temp_buffers` được sử dụng cho các bảng tạm thời. Thiết lập mặc định là 8 MB.

## Client Process

Client Process đề cập đến background process được chỉ định cho mội kết nối với người dùng. Thông thường, postmaster process sẽ phân ra một tiến trình con dành riêng phục vụ cho kết nối người dùng.

# Cấu trúc database

![object](/assets/img/postgresql/object.jpg)

Dưới đây là các khái niệm quan trọng cần biết khi tìm hiểu cấu trúc cơ sở dữ liệu của PostgreSQL.

## Database cluster

Là đơn vị lưu trữ lớn nhất của một PostgreSQL database server. Database cluster được tạo ra bởi câu lệnh `initdb()`, bao gồm các files config (postgresql.conf, pg_hba.conf, ...), và tất cả các đối tượng lưu trữ đều nằm trong database cluster. 

## Databases

Là đơn vị lớn sau Database cluster. Để thực hiện được câu truy vấn, bạn phải truy cập vào một database nào đó. Khi `initdb()` thực thi, mặc định PostgreSQL sẽ tạo ra 3 csdl là **template0**, **template1** và **postgres**.

### template0

Là cơ sở dữ liệu mẫu. Không thể truy nhập và chỉnh sửa các đối tượng trong đó. Người dùng có thể tạo database mới dựa trên template0 này bằng cách chỉ định TEMPLATE trong câu lệnh **CREATE DATABASE**
### template1

Là cơ sở dữ liệu mẫu. Người dùng có thể truy nhập và chỉnh sửa các đối tượng trong đó. Khi thực hiện câu lệnh "CREATE DATABASE", PostgreSQL sẽ copy template1 này để tạo database mới.

### postgres

Cơ sở dữ liệu mặc định của PostgreSQL khi tạo database cluster.

## Tablespace

Là đơn vị lưu trữ dữ liệu về phương diện vật lý bên dưới database. Thông thường dữ liệu vật lý được lưu trữ tại thư mục dữ liệu (nơi ta chỉ định lúc ta tạo database cluster initdb() -D). Nhưng có một phương pháp lưu trữ dữ liệu ngoài phân vùng này, nhờ sử dụng chức năng TABLESPACE. Khi tạo một TABLESPACE tức là ta đã tạo ra một vùng lưu trữ dữ liệu mới độc lập với dữ liệu bên dưới thư mục dữ liệu. Điều này giảm thiểu được disk I/O cho phân vùng thư mục dữ liệu (nếu trong các hệ thống cấu hình RAID, hay hệ thống có 1 đĩa cứng thì không có hiệu quả).

## Schema

Là đơn vị lưu trữ bên dưới database, quản lý dữ liệu dưới dạng logic. Mặc định trong mỗi database có một schema cho người sử dụng, đó là schema public. Ta có thể tạo schema bằng câu lệnh **CREATE SCHEMA**. Đặc điểm của 1 schema như sau:

- Có thể sử dụng tên trùng với schema ở database khác nhưng không trùng tên trên cùng database.
- Ngoài TABLESPACE và user ra, schema có thể chứa hầu hết các đối tượng còn lại (như table, index, sequence, constraint...)
để truy cập schema ta có thể thêm tên schema vào phía trước đối tượng muốn truy cập hoặc sử dụng tham số `search_path` để thay đổi schema truy cập hiện tại.
- Schema có thể sử dụng với các mục đích như tăng cường bảo mật, quản lý dữ liệu dễ dàng hơn.

## Bảng(table)

Bảng là đối tượng lưu trữ dữ liệu từ người dùng. Một bảng bao gồm 0 hoặc nhiều cột (column) tương ứng với từng kiểu dữ liệu khác nhau của PostgreSQL.
Tổng quan có 3 kiểu tables mà PostgreSQL hỗ trợ, đó là:

- **unlogged table**: là kiểu table mà các thao tác đối với bảng dữ liệu này không được lưu trữ vào WAL. Tức là không có khả năng phục hồi nếu bị corrupt.
- **temporary table**: là kiểu table chỉ được tạo trong phiên làm việc đó. Khi connection bị ngắt, nó sẽ tự động mất đi.
- **table thông thường**: Khác với 2 kiểu table trên, là loại table thông thường để lưu trữ dữ liệu. Có khả năng phục hồi khi bị corrupt và tồn tại vĩnh viễn nếu không có thao tác xóa bỏ nào.
