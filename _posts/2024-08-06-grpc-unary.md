---
layout: post
title: gRPC Unary
categories: gRPC
---

### Tổng quan

*link: https://aithietke.com/grpc-grpc-unary/*

- **Unary** là mô hình request & response đơn giản nhất. Client sẽ gửi request đến server và server sẽ xử lý yêu cầu sau đó phản hòi lại cho client. Cuộc gọi này có thể là đồng bộ (**blocking synchronous call**)  hoặc không đồng bộ (**non-blocking asynchronous**).

### Protobuf – Service Definition (Định nghĩa dịch vụ)

- Khi biết nghiệp vụ, request và response cần là gì, chúng ta sẽ định nghĩa service xử lý nghiệp vụ cho chúng. Phương thức **findFactorial** được implements ở phía Server nhận kiểu đầu vào và trả kiểu dữ liệu đầu ra được mong đợi. Mặc định, nếu chúng ta không sử dụng từ khóa **Stream** thì nó là kiểu unary. File .proto định nghĩa service trong ví dụ này như sau:

```java

syntax = "proto3";

package factorial;

option java_package = "example.calculator";
option java_multiple_files = true;

message NumberRequest {
  int32 number = 1;
}

message NumberResponse {
  int64 result = 1;
}

service CalculatorService {
  // unary
  rpc findFactorial(NumberRequest) returns (NumberResponse) {};
}

```

Khi chúng ta chạy lệnh maven dưới đây, maven sẽ tự động tạo code cho client application và server application bằng công cụ protoc.

```java
mvn clean compile
```

- **Class CalculatorServiceImplBase** là abstract class được tạo tự động khi gen code cần được phía Server implements để triển khai nghiệp vụ.
- Tương tự như vậy class **CalculatorServiceStub** là class mà **phía client application** sử dụng để gửi yêu cầu **đến server**.

Như chúng ta thấy từ một file định nghĩa dịch vụ định dạng .proto và một lệnh maven là gen ra được các class phía client và server có thể giao tiếp với nhau.


## gRPC Unary API – Triển khai trên Server Side

- **Service Implmentation:**  Đầu tiên là class sẽ kế thừa abtract class **CalculatorServiceImplBase** để implements phương thức **findFactorial** để triển khai nghiệp vụ. Cách thực hiện sẽ là: server sẽ nhận được kiểu dữ liệu đầu vào mà chúng ta đã xác định bằng file protobuf trước đó.

- **Success Response:**  tìm giai thừa cho một số đã cho và gửi lại kết quả bằng cách sử dụng **responseObserver**. Dữ liệu trả về thông qua phương thức **onNext** tới client đang gọi và cũng thông báo cho client rằng server đã hoàn thành công việc bằng cách gọi phương thức **onCompleted**.

- **Error Response:** trong trường hợp có bất kỳ lỗi nào trong quá trình xử lý, server sẽ sử dụng phương thức **onError** để thông báo cho client. 

```java
public class FactorialService extends FactorialServiceGrpc.FactorialServiceImplBase {

    @Override
    public void findFactorial(NumberRequest request, StreamObserver<NumberResponse> responseObserver) {
        int number = request.getNumber();
        long result = this.factorial(number);
        NumberResponse response = NumberResponse.newBuilder()
                .setResult(result)
                .build();

        responseObserver.onNext(response);
        responseObserver.onCompleted();
    }

    private long factorial(int number){
        if(number == 0)
            return 1;
        return number * factorial(number - 1);
    }

}
```

Sau khi implements xong, chúng ta cần thực hiện cấu hình Server để start gRPC server.

```java
public class FactorialGrpcServer {

    public static void main(String[] args) throws IOException, InterruptedException {

        // build gRPC server
        Server server = ServerBuilder.forPort(6566)
                .addService(new FactorialService())
                .build();

        // start
        server.start();

        // shutdown hook
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            System.out.println("gRPC server is shutting down!");
            server.shutdown();
        }));

        server.awaitTermination();

    }

}
```

Bây giờ thì gRPC server đã sẵn sàng. Chúng ta sẽ qua Client Side.

## gRPC Blocking Unary Call

Trên phía Client chúng ta cần thực hiện các bước sau để gửi request và nhận lại response.

- **Tạo kênh (create channel):** Trước tiên, client apllication phải tạo một kênh kết nối với gRPC server.
- **Stub:** Tiếp theo client apllication sẽ sử dụng **blocking stub / non-blocking** stub tùy thuộc vào yêu cầu để thực hiện truyền tham số và gửi request.

Trong ví dụ này chúng ta sẽ tạo một class JUnit để hoạt động như một gRPC client. Hãy lưu ý rằng client application (hay gRPC client) có thể là bất cứ ngôn ngữ gì. Nó thậm chí có thể là một microservice khác.

```java

public class UnaryServiceTest {

    private ManagedChannel channel;
    private FactorialServiceGrpc.FactorialServiceBlockingStub clientStub;

    @Before
    public void setup(){
        this.channel = ManagedChannelBuilder.forAddress("localhost", 6565)
                .usePlaintext()
                .build();
        this.clientStub = FactorialServiceGrpc.newBlockingStub(channel);
    }

    @Test
    public void unaryServiceTest(){
        // build the request object
        NumberRequest numberRequest = NumberRequest.newBuilder()
                .setNumber(5)
                .build();

        // receive the response
        NumberResponse numberResponse = this.clientStub.findFactorial(numberRequest);

        //check the result
        Assert.assertEquals(120, numberResponse.getResult());
    }

    @After
    public void teardown(){
        this.channel.shutdown();
    }

}

```

Chúng ta có thể xác minh request thành công đó là được kết quả như mong đợi.

## gRPC Async Unary Call

Nếu request khiến server application tốn một lượng thời gian để xử lý, chúng ta có thể đặt request này thành **non-blocking stub** như hình dưới đây: 

- Bước đầu tiên, chúng ta cần triển khai **StreamObserver**.

```java

public class NumberResponseObserver implements StreamObserver<NumberResponse> {

    @Override
    public void onNext(NumberResponse output) {
        System.out.println("=========== server onNext ===========");
        System.out.println("Received : " + output.getResult());
    }

    @Override
    public void onError(Throwable throwable) {
        System.out.println("=========== server onError ===========");
        throwable.getMessage();
        throwable.printStackTrace();
    }

    @Override
    public void onCompleted() {
        System.out.println("=========== server onCompleted ===========");
    }

}

```

- Sau đó, chúng ta tạo một **asynchronous stub** và truyền implemetation trên vào phương thức xử lý chính.

```java

public class UnaryServiceTest {

    private ManagedChannel channel;
    private CalculatorServiceGrpc.CalculatorServiceStub clientStub;

    @Before
    public void setup(){
        this.channel = ManagedChannelBuilder.forAddress("localhost", 6565)
                .usePlaintext()
                .build();
        this.clientStub = CalculatorServiceGrpc.newStub(channel);
    }

    @Test
    public void unaryServiceTest(){
        // build the request object
        Input input = Input.newBuilder()
                .setNumber(5)
                .build();

        // receive the response
        this.clientStub.findFactorial(input, new NumberResponseObserver());
    }

    @After
    public void teardown(){
        this.channel.shutdown();
    }
}

```

Bây giờ client apllication sẽ không đợi phản hồi từ server. Việc triển khai StreamObserver sẽ được thực thi không đồng bộ khi nhận được phản hồi từ server apllication.

## BloomRPC

Tương tự như Postman, BloomPRC được dùng để thử nghiệm các cuộc gọi gRPC. Hướng dẫn cài đặt có thể tham khảo: https://github.com/bloomrpc/bloomrpc

+ Sau khi cài đặt, chạy BloomRPC và upload file .proto
+ BloomRPC tự động hiển thị tất cả các phương thức định nghĩa trong file proto
+ Điền thông tin Server gRPC
+ Gửi yêu cầu và nhận phản hồi