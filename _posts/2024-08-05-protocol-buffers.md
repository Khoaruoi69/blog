---
layout: post
title: Protocol Buffers
categories: gRPC
---


## Protocol Buffers

- Protocol Buffers, thường được viết tắt là Protobuf, là một cơ chế tuần tự hóa dữ liệu được phát triển bởi Google.
- Protobuf cho phép bạn định nghĩa các cấu trúc dữ liệu và dịch vụ trong một định dạng độc lập với ngôn ngữ lập trình, sau đó tự động sinh ra mã nguồn cho các ngôn ngữ lập trình khác nhau để dễ dàng sử dụng các cấu trúc dữ liệu và dịch vụ đó.

## Các khái niệm chính

### Tệp .proto:

- Tệp **.proto** chứa các định nghĩa về cấu trúc dữ liệu (message) và các dịch vụ (service) trong định dạng văn bản. 
- Đây là nơi bạn mô tả các kiểu dữ liệu và cấu trúc mà bạn muốn sử dụng trong ứng dụng của mình.

### Cấu Trúc Dữ Liệu (Message):

- Trong Protobuf, bạn định nghĩa các cấu trúc dữ liệu bằng từ khóa message. Ví dụ:

```java
message Person {
    int32 id = 1;
    string name = 2;
    string email = 3;
}
```
- Ở đây, **Person** là một cấu trúc dữ liệu với ba trường: **id, name, và email**. Mỗi trường có một loại dữ liệu và một số thứ tự (tag) duy nhất để xác định trường đó trong dữ liệu đã tuần tự hóa.

### Dịch Vụ (Service):

- Bạn cũng có thể định nghĩa các dịch vụ gRPC trong tệp **.proto**. Ví dụ

```java
service PersonService {
    rpc GetPersonById (PersonRequest) returns (Person);
}

```

- Ở đây, **PersonService** có một phương thức **GetPersonById** nhận một **PersonRequest** và trả về một **Person**

### Sinh Mã Nguồn:

- Sau khi định nghĩa các cấu trúc dữ liệu và dịch vụ trong tệp **.proto**, bạn sử dụng công cụ **protoc (Protocol Buffers Compiler)** để sinh mã nguồn cho các ngôn ngữ lập trình khác nhau như Java, Python, C++, Go, và nhiều hơn nữa.
-  Điều này giúp tích hợp Protobuf vào ứng dụng của bạn mà không phải viết mã tuần tự hóa và giải tuần tự hóa thủ công.

## Các Lợi Ích của Protobuf

### Hiệu Suất Cao:

- Protobuf được thiết kế để hiệu quả trong việc tuần tự hóa và giải tuần tự hóa dữ liệu. Dữ liệu được tuần tự hóa với kích thước nhỏ hơn và tốc độ nhanh hơn so với các định dạng khác như JSON hoặc XML.

### Tính Nâng Cấp:

- Protobuf hỗ trợ việc thêm, xóa, và thay đổi các trường mà không làm hỏng khả năng tương thích ngược. Điều này giúp dễ dàng nâng cấp các cấu trúc dữ liệu mà không ảnh hưởng đến các ứng dụng đã sử dụng phiên bản trước đó

### Hỗ Trợ Đa Ngôn Ngữ:

- Protobuf cung cấp mã nguồn tự động cho nhiều ngôn ngữ lập trình, giúp việc tích hợp dễ dàng hơn.

### Định Dạng Nhỏ Gọn:

- Dữ liệu tuần tự hóa bằng Protobuf thường nhỏ hơn so với JSON hoặc XML, giúp tiết kiệm băng thông và không gian lưu trữ.

## Ví Dụ Sử Dụng

Giả sử bạn có một ứng dụng cần gửi thông tin người dùng từ một máy chủ đến một ứng dụng khách. Bạn có thể định nghĩa một thông điệp **User** trong tệp **.proto** và sử dụng **Protobuf** để tuần tự hóa dữ liệu đó trước khi gửi nó qua mạng. Ứng dụng khách sau đó sẽ giải tuần tự hóa dữ liệu nhận được để truy cập các thông tin người dùng.

```java
syntax = "proto3";

message User {
    int32 id = 1;
    string name = 2;
    string email = 3;
}

```

Bạn có thể sinh mã nguồn cho Java và sử dụng các lớp Java tự động tạo ra để tạo đối tượng User, tuần tự hóa đối tượng này, gửi qua mạng, và giải tuần tự hóa ở phía nhận.
