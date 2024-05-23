---
layout: post
title: API
categories: Discussion
---

### What is an API ?


#### API là gì ?

- API là cơ chế cho phép 2 thành phần phần mềm giao tiếp với nhau bằng cách sử dụng chung 1 bộ định nghĩa và giao thức. 

*VD:* ví hệ thống dự báo thời tiết hằng ngày của cục thời tiết chứa dữ liệu thời tiết. Ứng dụng thời tiết tiết trên điện thoại "nói chuyện" với hệ thống này
thông qua API và hiển thị cho bạn thông tin cập nhật thời tiết hằng ngày.


#### API viết tắt từ gì ?

- API là viết tắt của Application Program Interface. 
Trong ngữ của API được hiểu:
+ "Application": dùng để chỉ bất kỳ phần mềm nào có chức năng riêng biệt.
+ "Interface": có thể được coi là một contract (*hợp đồng*) giữa hai application. Contract này xác định cách hai người giao tiếp với nhau bằng cách
sử dụng các yêu cầu (**request**) và phản hổi (**response**). 

### API hoạt động như thế nào ?

- Kiến trúc API thường được giải thích dưới dạng máy khách (**client**) và máy chủ (**server**). Ứng dụng gửi yêu cầu là máy khách và ứng dụng gửi phản hồi được
gọi là máy chủ. 

- Có 4 cách khác nhau mà API có thể hoạt động tùy thuộc vào thời điểm và lý do chúng được tạo ra.

+ **SOAP APIs**: Các API này sử dụng Giao thức truy cập đối tượng đơn giản(**Simple Object Access Protocol.**). Client and Server trao đổi với nhau bằng XML.
Đây là một API kém linh hoạt nhưng phổ biến trong quá khứ.

+ **RPC APIs**: Các API này được gọi là Cuộc gọi thủ tục từ xa (**Remote Procedure Call**). Client hoàn thành một chức năng (*function*) (hoặc thủ tục (*procedure*)) trên Server, và Server gửi đầu ra (*output*) cho Client.

+ **Websocket API**: API Websocket là một phương pháp phát triển API web hiện đại khác sử dụng các đối tượng JSON để truyền dữ liệu. API WebSocket hỗ trợ giao tiếp hai chiều giữa ứng dụng khách và máy chủ. Máy chủ có thể gửi tin nhắn gọi lại đến các máy khách được kết nối, giúp nó hiệu quả hơn API REST.

+ **REST API**: Đây là những API phổ biến và linh hoạt nhất được tìm thấy trên web hiện nay. Máy khách gửi yêu cầu đến máy chủ dưới dạng dữ liệu (*The client sends requests to the server as data.*). Máy chủ sử dụng đầu vào của máy khách này để bắt đầu các chức năng nội bộ và trả lại dữ liệu đầu ra cho client. Hãy xem xét các API REST chi tiết hơn bên dưới. (*The server uses this client input to start internal functions and returns output data back to the client. Let’s look at REST APIs in more detail below.*)

https://aws.amazon.com/what-is/api/

https://aws.amazon.com/vi/compare/the-difference-between-rpc-and-rest/

https://aws.amazon.com//vi/what-is/restful-api/