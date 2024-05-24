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


### REST API là gì ?

- REST viết tắt của từ Representational State Transfer (*Chuyển giao trạng thái đại diện.*). REST xác định một tập hợp các hàm như GET, PUT, DELETE, v.v. mà máy khách có thể sử dụng để truy cập dữ liệu máy chủ. Máy khách và máy chủ trao đổi dữ liệu bằng HTTP. (*Clients and servers exchange data using HTTP.*)

- Tính năng chính của REST API là tính không trạng thái (*The main feature of REST API is statelessness.*). Không trạng thái có nghĩa là máy chủ không lưu dữ liệu khách hàng giữa các yêu cầu. (* Statelessness means that servers do not save client data between requests*). Yêu cầu của khách hàng tới máy chủ tương tự như các URL bạn nhập vào trình duyệt để truy cập một trang web. (*Client requests to the server are similar to URLs you type in your browser to visit a website*). Phản hồi từ máy chủ là dữ liệu đơn giản, không có hiển thị đồ họa thông thường của trang web. (*The response from the server is plain data, without the typical graphical rendering of a web page.*)

### Web API là gì ?

- Web API or Web Service API là giao diện xử lý ứng dụng giữa máy chủ web và trình duyệt web (*A Web API or Web Service API is an application processing interface between a web server and web browser*). Tất cả web service là API nhưng không phải tất cả API là web services. REST API là loại đặt biệt của Web API đặc 
biệt xử dụng kiểu kiến trúc tiêu chuẩn được giải thích ở trên. 

- Các thuật ngữ khác nhau xung quanh API, like Java API or service APIs, tồn tại bởi vì về mặt lịch sử , các API đã được tạo ra trước web toàn cầu. API web hiện tại là REST API và các thuật ngữ có thể thay thế cho nhau. (*The different terms around APIs, like Java API or service APIs, exist because historically, APIs were created before the world wide web. Modern web APIs are REST APIs and the terms can be used interchangeably.*)

### Tích hợp API là gì ?  (*What is API integrations ?*)

- Tích hợp API là các thành phần phần mềm tự động cập nhật dữ liệu giữa máy khách và máy chủ. (*API integrations are software components that automatically update data between clients and servers*).  Một số ví dụ về tích hợp API là khi tự động đồng bộ hóa dữ liệu lên đám mây từ thư viện hình ảnh trên điện thoại của bạn, hoặc ngày giờ tự động đồng bộ hóa trên máy tính xách tay của bạn khi bạn di chuyển đến múi giờ khác. Doanh nghiệp cũng có thể sử dụng chúng để tự động hóa hiệu quả nhiều chức năng của hệ thống.

### Lợi ích của API REST là gì? (*What are the benefits of REST APIs*)

- REST APIs offer four main benefits:

+ 1. Integration (*hội nhập*)
 
 API được sử dụng để tích hợp các ứng dụng mới với hệ thống phần mềm hiện có. (*APIs are used to integrate new applications with existing software systems*)
 Điều này làm tăng tốc độ phát triển vì mỗi chức năng không cần phải viết từ đầu. (*This increases development speed because each functionality doesn’t have to be written from scratch*). Bạn có thể sử dụng API để tận dụng mã hiện có. (*You can use APIs to leverage existing code.*)

 + 2. Innovation (*Sự đổi mới*)

 Toàn bộ ngành công nghiệp có thể thay đổi khi có sự xuất hiện của một ứng dụng mới. (*Entire industries can change with the arrival of a new app.*)
 Doanh nghiệp cần phản ứng nhanh chóng và hỗ trợ triển khai nhanh chóng các dịch vụ đổi mới. (*Businesses need to respond quickly and support the rapid deployment of innovative services*). Họ có thể thực hiện việc này bằng cách thực hiện các thay đổi ở cấp API mà không cần phải viết lại toàn bộ mã. (*They can do this by making changes at the API level without having to re-write the whole code.*)

 + 3. Expansion (*Sự bành chướng*)

 API mang đến cơ hội duy nhất cho các doanh nghiệp đáp ứng nhu cầu của khách hàng trên các nền tảng khác nhau. (*APIs present a unique opportunity for businesses to meet their clients’ needs across different platforms.*). Ví dụ: API bản đồ cho phép tích hợp thông tin bản đồ qua trang web, Android, iOS, v.v. Bất kỳ doanh nghiệp nào cũng có thể cấp quyền truy cập tương tự vào cơ sở dữ liệu nội bộ của họ bằng cách sử dụng API miễn phí hoặc trả phí. (*For example, maps API allows map information integration via websites, Android,iOS, etc. Any business can give similar access to their internal databases by using free or paid APIs.*)

 + 4. Ease of maintenance (*Dễ bảo trì*)

 API hoạt động như một cổng kết nối giữa hai hệ thống. (*The API acts as a gateway between two systems. *)
 Mỗi hệ thống có nghĩa vụ thực hiện các thay đổi nội bộ để API không bị ảnh hưởng. (* Each system is obliged to make internal changes so that the API is not impacted.*)
 Bằng cách này, mọi thay đổi mã trong tương lai của một bên sẽ không ảnh hưởng đến bên kia.(*This way, any future code changes by one party do not impact the other party.*)


 ### Các loại API khác nhau là gì ? (*What are the different types of APIs ?*)

API được phân loại theo kiến ​​trúc và phạm vi sử dụng của chúng. Chúng ta đã khám phá các loại kiến ​​trúc API chính, vì vậy hãy xem phạm vi sử dụng:

- **Private APIs:**  Đây là nội bộ của doanh nghiệp và chỉ được sử dụng để kết nối các hệ thống và dữ liệu trong doanh nghiệp.
- **Public APIs** Chúng được mở cho công chúng và bất kỳ ai cũng có thể sử dụng. Có thể có hoặc không có một số ủy quyền và chi phí liên quan đến các loại API này.
- **Partner APIs** Những điều này chỉ có thể được truy cập bởi các nhà phát triển bên ngoài được ủy quyền để hỗ trợ quan hệ đối tác giữa doanh nghiệp với doanh nghiệp.
- **Composite APIs** Chúng kết hợp hai hoặc nhiều API khác nhau để giải quyết các yêu cầu hoặc hành vi phức tạp của hệ thống

### Điểm cuối API là gì và tại sao nó quan trọng? (*What is an API endpoint and why is it important?*)

- Điểm cuối API là điểm tiếp xúc cuối cùng trong hệ thống giao tiếp API. (*API endpoints are the final touchpoints in the API communication system*)
Chúng bao gồm URL máy chủ, dịch vụ và các vị trí kỹ thuật số cụ thể khác từ đó thông tin được gửi và nhận giữa các hệ thống. (* These include server URLs, services, and other specific digital locations from where information is sent and received between systems*). Điểm cuối API rất quan trọng đối với doanh nghiệp vì hai lý do chính
(*API endpoints are critical to enterprises for two main reasons: *)

+ 1. Security

Điểm cuối API khiến hệ thống dễ bị tấn công. Giám sát API là rất quan trọng để ngăn chặn việc sử dụng sai mục đích.
(*API endpoints make the system vulnerable to attack. API monitoring is crucial for preventing misuse.*)

+ 2. Performance

Điểm cuối API, đặc biệt là điểm cuối có lưu lượng truy cập cao, có thể gây tắc nghẽn và ảnh hưởng đến hiệu suất hệ thống.
(*API endpoints, especially high traffic ones, can cause bottlenecks and affect system performance.*)

### Làm cách nào để bảo mật API REST? (*How to secure a REST API ?*)

Tất cả các API phải được bảo mật thông qua xác thực và giám sát thích hợp. Hai cách chính để bảo mật API REST bao gồm:
(*All APIs must be secured through proper authentication and monitoring. The two main ways to secure REST APIs include:*)

+ 1. Authentication tokens 

Chúng được sử dụng để ủy quyền cho người dùng thực hiện lệnh gọi API. (*These are used to authorize users to make the API call.*)
Mã thông báo xác thực kiểm tra xem người dùng có đúng là người họ xác nhận hay không và họ có quyền truy cập vào API cụ thể đó không.
(*Authentication tokens check that the users are who they claim to be and that they have access rights for that particular API call.*)
Ví dụ: khi bạn đăng nhập vào máy chủ email, ứng dụng email của bạn sẽ sử dụng mã thông báo xác thực để truy cập an toàn.
(*For example, when you log in to your email server, your email client uses authentication tokens for secure access.*)

+ 2. API keys

Khóa API xác minh chương trình hoặc ứng dụng thực hiện lệnh gọi API. (*API keys verify the program or application making the API call.*)
Họ xác định ứng dụng và đảm bảo ứng dụng có quyền truy cập cần thiết để thực hiện lệnh gọi API cụ thể. (*They identify the application and ensure it has the access rights required to make the particular API call*). Khóa API không an toàn như mã thông báo nhưng chúng cho phép giám sát API để thu thập dữ liệu về việc sử dụng
(*API keys are not as secure as tokens but they allow API monitoring in order to gather data on usage*). Bạn có thể nhận thấy một chuỗi dài các ký tự và số trong URL trình duyệt của mình khi truy cập các trang web khác nhau. (* You may have noticed a long string of characters and numbers in your browser URL when you visit different websites.*)
Chuỗi này là khóa API mà trang web sử dụng để thực hiện lệnh gọi API nội bộ. (*This string is an API key the website uses to make internal API calls.*)


### API Gateway là gì ? (*What is an API gateway ?*)

Cổng API là một công cụ quản lý API dành cho khách hàng doanh nghiệp sử dụng nhiều dịch vụ phụ trợ. (*An API Gateway is an API management tool for enterprise clients that use a broad range of back-end services.*). Cổng API thường xử lý các tác vụ phổ biến như xác thực người dùng, thống kê và quản lý tốc độ có thể áp dụng trên tất cả các lệnh gọi API. (*API gateways typically handle common tasks like user authentication, statistics, and rate management that are applicable across all API calls.*)

Amazon API Gateway là một dịch vụ được quản lý hoàn toàn giúp các nhà phát triển dễ dàng tạo, xuất bản, duy trì, giám sát và bảo mật API ở mọi quy mô. (*Amazon API Gateway is a fully managed service that makes it easy for developers to create, publish, maintain, monitor, and secure APIs at any scale.*)
Nó xử lý tất cả các nhiệm vụ liên quan đến việc chấp nhận và xử lý hàng nghìn lệnh gọi API đồng thời, bao gồm quản lý lưu lượng, hỗ trợ CORS, ủy quyền và kiểm soát truy cập, điều tiết, giám sát và quản lý phiên bản API. (*It handles all the tasks involved in accepting and processing thousands of concurrent API calls, including traffic management, CORS support, authorization, and access control, throttling, monitoring, and API version management.*)


### GraphQl là gì ?

GraphQL là ngôn ngữ truy vấn được phát triển riêng cho API. (*GraphQL is a query language that was developed specifically for APIs.*)
Nó ưu tiên cung cấp cho khách hàng chính xác dữ liệu họ yêu cầu và không hơn thế nữa. Nó được thiết kế để làm cho API nhanh chóng, linh hoạt và thân thiện với nhà phát triển. (*It prioritizes giving clients exactly the data they request and no more. It is designed to make APIs fast, flexible, and developer-friendly*)
Là một giải pháp thay thế cho REST, GraphQL cung cấp cho các nhà phát triển giao diện người dùng khả năng truy vấn nhiều cơ sở dữ liệu, vi dịch vụ và API bằng một điểm cuối GraphQL duy nhất. (*As an alternative to REST, GraphQL gives front-end developers the ability to query multiple databases, microservices, and APIs with a single GraphQL endpoint*).  Các tổ chức chọn xây dựng API bằng GraphQL vì nó giúp họ phát triển ứng dụng nhanh hơn. (*Organizations choose to build APIs with GraphQL because it helps them develop applications faster.*)

AWS AppSync là một dịch vụ được quản lý toàn phần giúp dễ dàng phát triển API GraphQL bằng cách xử lý khối lượng công việc nặng nhọc trong việc kết nối an toàn với các nguồn dữ liệu như AWS DynamoDB, AWS Lambda, v.v. AWS AppSync có thể đẩy các bản cập nhật dữ liệu theo thời gian thực qua Websockets tới hàng triệu khách hàng. Đối với các ứng dụng di động và web, AppSync cũng cung cấp quyền truy cập dữ liệu cục bộ khi thiết bị ngoại tuyến. Sau khi được triển khai, AWS AppSync sẽ tự động tăng giảm quy mô công cụ thực thi API GraphQL để đáp ứng khối lượng yêu cầu API.