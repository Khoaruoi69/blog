---
layout: post
title: Example gRPC
categories: gRPC
---


## Protobuf

```java
syntax = "proto3";

option java_multiple_files = true;

package com.devProblems;

message Book {
  int32 book_id = 1;
  string title = 2;
  float price = 3;
  int32 pages = 4;
  int32 author_id = 5;
}

message Author {
  int32 author_id = 1;
  string first_name = 2;
  string last_name = 3;
  string gender = 4;
  int32 book_id = 5;
}


service BookAuthorService {

  //unary - synchronous
  //client will send one request and server will respond with one response.
  rpc getAuthor(Author) returns(Author){}

  //sever streaming - Asynchronous
  //client will send one request and server will send stream of response to the client.
  rpc getBooksByAuthor(Author) returns(stream Book){}

  //client streaming - Asynchronous
  //client will stream of request and server will respond with one response.
  rpc getExpensiveBook(stream Book) returns(Book){}

  //bi-directional streaming - Asynchronous
  //client will send stream of request and server will respond with stream of response.
  rpc getBooksByGender(stream Book) returns (stream Book) {}
}
```

## Phần khai báo chung

```java

syntax = "proto3";

option java_multiple_files = true;

package com.devProblems;

```

- **syntax = "proto3"**: Chỉ định rằng tệp này sử dụng cú pháp của Protocol Buffers phiên bản 3.
- **option java_multiple_files = true**: Tùy chọn này cho biết rằng mã Java sinh ra sẽ được chia thành nhiều tệp.
- **package com.devProblems**: Đặt các thông điệp và dịch vụ vào trong gói com.devProblems khi sinh ra mã nguồn.

## Định nghĩa thông điệp (Messages)

### **Thông điệp 'Book'**

```java

message Book {
  int32 book_id = 1;
  string title = 2;
  float price = 3;
  int32 pages = 4;
  int32 author_id = 5;
}

```

- Book: Định nghĩa một thông điệp có tên là Book với các trường dữ liệu sau:

+ **int32 book_id = 1**: Mã sách (kiểu số nguyên 32-bit).
+ **string title = 2**: Tiêu đề sách (kiểu chuỗi).
+ **float price = 3**: Giá sách (kiểu số thực).
+ **int32 pages = 4**: Số trang sách (kiểu số nguyên 32-bit).
+ **int32 author_id = 5**: Mã tác giả (kiểu số nguyên 32-bit).

### **Thông điệp Author (tương tự)**

## Định nghĩa dịch vụ (Service)

```java

service BookAuthorService {

  //unary - synchronous
  //client will send one request and server will respond with one response.
  rpc getAuthor(Author) returns(Author){}

  //server streaming - Asynchronous
  //client will send one request and server will send stream of response to the client.
  rpc getBooksByAuthor(Author) returns(stream Book){}

  //client streaming - Asynchronous
  //client will stream of request and server will respond with one response.
  rpc getExpensiveBook(stream Book) returns(Book){}

  //bi-directional streaming - Asynchronous
  //client will send stream of request and server will respond with stream of response.
  rpc getBooksByGender(stream Book) returns (stream Book) {}
}

```

- **BookAuthorService:** Định nghĩa một dịch vụ có tên là **BookAuthorService** với các phương thức **RPC (Remote Procedure Call)** sau:

+ **rpc getAuthor(Author) returns(Author){}**:  Kiểu Unary - đồng bộ: Client gửi một yêu cầu và server đáp lại bằng một phản hồi.
+ **rpc getBooksByAuthor(Author) returns(stream Book){}**: Kiểu Server Streaming - bất đồng bộ: Client gửi một yêu cầu và server đáp lại bằng một luồng phản hồi (nhiều thông điệp Book).
+ **rpc getExpensiveBook(stream Book) returns(Book){}**: Kiểu Client Streaming - bất đồng bộ: Client gửi một luồng yêu cầu (nhiều thông điệp Book) và server đáp lại bằng một phản hồi.
+ **rpc getBooksByGender(stream Book) returns (stream Book) {}**: Kiểu Bi-directional Streaming - bất đồng bộ: Client và server gửi và nhận luồng thông điệp Book song song.


# grpc-service ( Book Author Server Service)

```java
@GrpcService
public class BookAuthorServerService extends BookAuthorServiceGrpc.BookAuthorServiceImplBase {

    @Override
    public void getAuthor(Author request, StreamObserver<Author> responseObserver) {
        TempDB.getAuthorsFromTempDb()
                .stream()
                .filter(author -> author.getAuthorId() == request.getAuthorId())
                .findFirst()
                .ifPresent(responseObserver::onNext);
        responseObserver.onCompleted();
    }

    @Override
    public void getBooksByAuthor(Author request, StreamObserver<Book> responseObserver) {
        TempDB.getBooksFromTempDb()
                .stream()
                .filter(book -> book.getAuthorId() == request.getAuthorId())
                .forEach(responseObserver::onNext);
        responseObserver.onCompleted();
    }

    @Override
    public StreamObserver<Book> getExpensiveBook(StreamObserver<Book> responseObserver) {
        return new StreamObserver<Book>() {
            Book expensiveBook = null;
            float priceTrack = 0;

            @Override
            public void onNext(Book book) {
                if (book.getPrice() > priceTrack) {
                    priceTrack = book.getPrice();
                    expensiveBook = book;
                }
            }

            @Override
            public void onError(Throwable throwable) {
                responseObserver.onError(throwable);
            }

            @Override
            public void onCompleted() {
                responseObserver.onNext(expensiveBook);
                responseObserver.onCompleted();
            }
        };
    }

    @Override
    public StreamObserver<Book> getBooksByGender(StreamObserver<Book> responseObserver) {
        return new StreamObserver<Book>() {
            List<Book> bookList = new ArrayList<>();

            @Override
            public void onNext(Book book) {
                TempDB.getBooksFromTempDb()
                        .stream()
                        .filter(bookFromDb -> bookFromDb.getAuthorId() == book.getAuthorId())
                        .forEach(bookList::add);
            }

            @Override
            public void onError(Throwable throwable) {
                responseObserver.onError(throwable);
            }

            @Override
            public void onCompleted() {
                bookList.forEach(responseObserver::onNext);
                responseObserver.onCompleted();
            }
        };
    }

```

 - Đoạn code trên triển khai một dịch vụ gRPC sử sụng thư viện gRPC trong Java. Dịch vụ này mở rộng **BookAuthorServiceGrpc.BookAuthorServiceImplBase** một lớp cơ sở được tạo ra từ tệp **.proto** đã định nghĩa trước đó.


 ## Khai báo lớp Annotation

 ```java
 @GrpcService
public class BookAuthorServerService extends BookAuthorServiceGrpc.BookAuthorServiceImplBase {

 ```

 - **@GrpcService**: Annotation này đánh dấu lớp như một dịch vụ gRPC.
 - **BookAuthorServerService** mở rộng **BookAuthorServiceGrpc.BookAuthorServiceImplBase** nghĩa là lớp này sẽ triển khai các phương thức RPC đã định nghĩa trong tệp **.proto**

 ## Phương thức getAuthor

 ```java
 @Override
public void getAuthor(Author request, StreamObserver<Author> responseObserver) {
    TempDB.getAuthorsFromTempDb()
            .stream()
            .filter(author -> author.getAuthorId() == request.getAuthorId())
            .findFirst()
            .ifPresent(responseObserver::onNext);
    responseObserver.onCompleted();
}
 
 ```

 - Phương thức này nhận một yêu cầu **Author** và trả về một phản hồi **Author**.
 - **TempDB.getAuthorsFromTempDb():** Giả định rằng phương thức này trả về danh sách các tác giả từ cơ sở dữ liệu tạm thời.
 - Stream các tác giả được lọc để tìm tác giả có authorId khớp với request.getAuthorId().
 - Nếu tìm thấy, tác giả được gửi về client bằng **responseObserver.onNext().**
 - Phương thức kết thúc bằng **responseObserver.onCompleted().**

 ## Phương thức getBooksByAuthor

```java
@Override
public void getBooksByAuthor(Author request, StreamObserver<Book> responseObserver) {
    TempDB.getBooksFromTempDb()
            .stream()
            .filter(book -> book.getAuthorId() == request.getAuthorId())
            .forEach(responseObserver::onNext);
    responseObserver.onCompleted();
}

```

- Phương thức này nhận một yêu cầu **Author** và trả về một luồng phản hồi **Book**.
- **TempDB.getBooksFromTempDb():** Giả định rằng phương thức này trả về danh sách các sách từ cơ sở dữ liệu tạm thời.
- **Stream** các sách được lọc để tìm các sách có authorId khớp với **request.getAuthorId()**.
- Mỗi sách được gửi về client bằng **responseObserver.onNext().**
- Phương thức kết thúc bằng **responseObserver.onCompleted().**

## Phương thức getExpensiveBook

```java
@Override
public StreamObserver<Book> getExpensiveBook(StreamObserver<Book> responseObserver) {
    return new StreamObserver<Book>() {
        Book expensiveBook = null;
        float priceTrack = 0;

        @Override
        public void onNext(Book book) {
            if (book.getPrice() > priceTrack) {
                priceTrack = book.getPrice();
                expensiveBook = book;
            }
        }

        @Override
        public void onError(Throwable throwable) {
            responseObserver.onError(throwable);
        }

        @Override
        public void onCompleted() {
            responseObserver.onNext(expensiveBook);
            responseObserver.onCompleted();
        }
    };
}

```

- Phương thức này nhận một luồng yêu cầu **Book** và trả về một phản hồi **Book**.
- Khởi tạo **expensiveBook** để lưu trữ sách đắt nhất và **priceTrack** để theo dõi giá cao nhất.
- Trong **onNext(Book book)**, nếu giá của sách hiện tại lớn hơn **priceTrack**, cập nhật **priceTrack** và **expensiveBook**.
- **onError(Throwable throwable)**: Xử lý lỗi bằng cách gửi lỗi tới client.
- **onCompleted()**: Khi luồng yêu cầu hoàn thành, gửi sách đắt nhất về client bằng **responseObserver.onNext()** và kết thúc phản hồi với **responseObserver.onCompleted()**.

## Phương thức getBooksByGender

```java

@Override
public StreamObserver<Book> getBooksByGender(StreamObserver<Book> responseObserver) {
    return new StreamObserver<Book>() {
        List<Book> bookList = new ArrayList<>();

        @Override
        public void onNext(Book book) {
            TempDB.getBooksFromTempDb()
                    .stream()
                    .filter(bookFromDb -> bookFromDb.getAuthorId() == book.getAuthorId())
                    .forEach(bookList::add);
        }

        @Override
        public void onError(Throwable throwable) {
            responseObserver.onError(throwable);
        }

        @Override
        public void onCompleted() {
            bookList.forEach(responseObserver::onNext);
            responseObserver.onCompleted();
        }
    };
}
```

- Phương thức này nhận và trả về một **luồng Book**.
- **onNext(Book book):** Với mỗi sách nhận được từ client, truy xuất danh sách sách từ cơ sở dữ liệu tạm thời và lọc các sách có authorId khớp với sách hiện tại, sau đó thêm vào bookList.
- **onError(Throwable throwable):** Xử lý lỗi bằng cách gửi lỗi tới client.
- **onCompleted():** Khi luồng yêu cầu hoàn thành, gửi tất cả các sách trong bookList về client bằng **responseObserver.onNext()** và kết thúc phản hồi với **responseObserver.onCompleted()**.

# client-service ( BookAuthorClientService)

```java
@Service
public class BookAuthorClientService {

    @GrpcClient("grpc-devproblems-service")
    BookAuthor ServiceGrpc.BookAuthorServiceBlockingStub synchronousClient;

    @GrpcClient("grpc-devproblems-service")
    BookAuthorServiceGrpc.BookAuthorServiceStub asynchronousClient;

    public Map<Descriptors.FieldDescriptor, Object> getAuthor(int authorId) {
        Author authorRequest = Author.newBuilder().setAuthorId(authorId).build();
        Author authorResponse = synchronousClient.getAuthor(authorRequest);
        return authorResponse.getAllFields();
    }

    public List<Map<Descriptors.FieldDescriptor, Object>> getBooksByAuthor(int authorId) throws InterruptedException {
        final Author authorRequest = Author.newBuilder().setAuthorId(authorId).build();
        final CountDownLatch countDownLatch = new CountDownLatch(1);
        final List<Map<Descriptors.FieldDescriptor, Object>> response = new ArrayList<>();
        asynchronousClient.getBooksByAuthor(authorRequest, new StreamObserver<Book>() {
            @Override
            public void onNext(Book book) {
                response.add(book.getAllFields());
            }

            @Override
            public void onError(Throwable throwable) {
                countDownLatch.countDown();
            }

            @Override
            public void onCompleted() {
                countDownLatch.countDown();
            }
        });
        boolean await = countDownLatch.await(1, TimeUnit.MINUTES);
        return await ? response : Collections.emptyList();
    }

    public Map<String, Map<Descriptors.FieldDescriptor, Object>> getExpensiveBook() throws InterruptedException {
        final CountDownLatch countDownLatch = new CountDownLatch(1);
        final Map<String, Map<Descriptors.FieldDescriptor, Object>> response = new HashMap<>();
        StreamObserver<Book> responseObserver = asynchronousClient.getExpensiveBook(new StreamObserver<Book>() {
            @Override
            public void onNext(Book book) {
                response.put("Expensive Book", book.getAllFields());
            }

            @Override
            public void onError(Throwable throwable) {
                countDownLatch.countDown();
            }

            @Override
            public void onCompleted() {
                countDownLatch.countDown();
            }
        });
        TempDB.getBooksFromTempDb().forEach(responseObserver::onNext);
        responseObserver.onCompleted();
        boolean await = countDownLatch.await(1, TimeUnit.MINUTES);
        return await ? response : Collections.emptyMap();
    }

    public List<Map<Descriptors.FieldDescriptor, Object>> getBooksByGender(String gender) throws InterruptedException {
        final CountDownLatch countDownLatch = new CountDownLatch(1);
        final List<Map<Descriptors.FieldDescriptor, Object>> response = new ArrayList<>();
        StreamObserver<Book> responseObserver = asynchronousClient.getBooksByGender(new StreamObserver<Book>() {
            @Override
            public void onNext(Book book) {
                response.add(book.getAllFields());
            }

            @Override
            public void onError(Throwable throwable) {
                countDownLatch.countDown();
            }

            @Override
            public void onCompleted() {
                countDownLatch.countDown();
            }
        });
        TempDB.getAuthorsFromTempDb()
                .stream()
                .filter(author -> author.getGender().equalsIgnoreCase(gender))
                .forEach(author -> responseObserver.onNext(Book.newBuilder().setAuthorId(author.getAuthorId()).build()));
        responseObserver.onCompleted();
        boolean await = countDownLatch.await(1, TimeUnit.MINUTES);
        return await ? response : Collections.emptyList();
    }

```

- Đoạn mã này triển khai một dịch vụ khách hàng gRPC trong Java, sử dụng client đong bộ và bất đồng bộ để gọi các dịch vụ gRPC đã được định nghĩa trước đó. 

## Khai báo lớp và các đối tượng gRPC Client

```java
@Service
public class BookAuthorClientService {

    @GrpcClient("grpc-devproblems-service")
    BookAuthorServiceGrpc.BookAuthorServiceBlockingStub synchronousClient;

    @GrpcClient("grpc-devproblems-service")
    BookAuthorServiceGrpc.BookAuthorServiceStub asynchronousClient;
}
```

- **@Service:** Annotation này đánh dấu lớp như một dịch vụ trong Spring.
- **@GrpcClient("grpc-devproblems-service"):** Annotation này đánh dấu các trường là client gRPC, kết nối tới dịch vụ có tên grpc-devproblems-service.
- **BookAuthorServiceGrpc.BookAuthorServiceBlockingStub synchronousClient:** Client đồng bộ để thực hiện các gọi RPC đồng bộ.
- **BookAuthorServiceGrpc.BookAuthorServiceStub asynchronousClient:** Client bất đồng bộ để thực hiện các gọi RPC bất đồng bộ.

## Phương thức getAuthor

```java
public Map<Descriptors.FieldDescriptor, Object> getAuthor(int authorId) {
    Author authorRequest = Author.newBuilder().setAuthorId(authorId).build();
    Author authorResponse = synchronousClient.getAuthor(authorRequest);
    return authorResponse.getAllFields();
}
```

- Tạo một yêu cầu **Author** với **authorId** đã cho.
- Sử dụng client đồng bộ **(synchronousClient)** để gọi **RPC getAuthor**.
- Trả về tất cả các trường của phản hồi **Author** dưới dạng một Map.

## Phương thức getBooksByAuthor

```java
public List<Map<Descriptors.FieldDescriptor, Object>> getBooksByAuthor(int authorId) throws InterruptedException {
    final Author authorRequest = Author.newBuilder().setAuthorId(authorId).build();
    final CountDownLatch countDownLatch = new CountDownLatch(1);
    final List<Map<Descriptors.FieldDescriptor, Object>> response = new ArrayList<>();
    asynchronousClient.getBooksByAuthor(authorRequest, new StreamObserver<Book>() {
        @Override
        public void onNext(Book book) {
            response.add(book.getAllFields());
        }

        @Override
        public void onError(Throwable throwable) {
            countDownLatch.countDown();
        }

        @Override
        public void onCompleted() {
            countDownLatch.countDown();
        }
    });
    boolean await = countDownLatch.await(1, TimeUnit.MINUTES);
    return await ? response : Collections.emptyList();
}

```
- Tạo một yêu cầu **Author** với **authorId** đã cho.
- Sử dụng client bất đồng bộ **(asynchronousClient)** để gọi RPC getBooksByAuthor.
- **StreamObserver<Book>** xử lý phản hồi, thêm từng **Book** vào danh sách response.
- Sử dụng **CountDownLatch** để đồng bộ hóa và chờ cho đến khi tất cả các phản hồi được xử lý hoặc thời gian chờ một phút kết thúc.
- Trả về danh sách các sách, hoặc danh sách rỗng nếu hết thời gian chờ.

## Phương thức getExpensiveBook

```java
public Map<String, Map<Descriptors.FieldDescriptor, Object>> getExpensiveBook() throws InterruptedException {
    final CountDownLatch countDownLatch = new CountDownLatch(1);
    final Map<String, Map<Descriptors.FieldDescriptor, Object>> response = new HashMap<>();
    StreamObserver<Book> responseObserver = asynchronousClient.getExpensiveBook(new StreamObserver<Book>() {
        @Override
        public void onNext(Book book) {
            response.put("Expensive Book", book.getAllFields());
        }

        @Override
        public void onError(Throwable throwable) {
            countDownLatch.countDown();
        }

        @Override
        public void onCompleted() {
            countDownLatch.countDown();
        }
    });
    TempDB.getBooksFromTempDb().forEach(responseObserver::onNext);
    responseObserver.onCompleted();
    boolean await = countDownLatch.await(1, TimeUnit.MINUTES);
    return await ? response : Collections.emptyMap();
}

```

- Sử dụng **client bất đồng bộ** để gọi **RPC getExpensiveBook**.
- StreamObserver<Book> xử lý phản hồi, lưu trữ sách đắt nhất trong response.
- Gửi tất cả các sách từ cơ sở dữ liệu tạm thời (TempDB) qua responseObserver.
- Sử dụng **CountDownLatch** để đồng bộ hóa và chờ cho đến khi tất cả các phản hồi được xử lý hoặc thời gian chờ một phút kết thúc.
- Trả về sách đắt nhất dưới dạng một Map, hoặc một Map rỗng nếu hết thời gian chờ.

## Phương thức getBooksByGender

```java
public List<Map<Descriptors.FieldDescriptor, Object>> getBooksByGender(String gender) throws InterruptedException {
    final CountDownLatch countDownLatch = new CountDownLatch(1);
    final List<Map<Descriptors.FieldDescriptor, Object>> response = new ArrayList<>();
    StreamObserver<Book> responseObserver = asynchronousClient.getBooksByGender(new StreamObserver<Book>() {
        @Override
        public void onNext(Book book) {
            response.add(book.getAllFields());
        }

        @Override
        public void onError(Throwable throwable) {
            countDownLatch.countDown();
        }

        @Override
        public void onCompleted() {
            countDownLatch.countDown();
        }
    });
    TempDB.getAuthorsFromTempDb()
            .stream()
            .filter(author -> author.getGender().equalsIgnoreCase(gender))
            .forEach(author -> responseObserver.onNext(Book.newBuilder().setAuthorId(author.getAuthorId()).build()));
    responseObserver.onCompleted();
    boolean await = countDownLatch.await(1, TimeUnit.MINUTES);
    return await ? response : Collections.emptyList();
}

```
- Sử dụng client bất đồng bộ để gọi RPC **getBooksByGender**.
- StreamObserver<Book> xử lý phản hồi, thêm từng **Book** vào danh sách **response**.
- Lọc các tác giả từ cơ sở dữ liệu tạm thời theo gender, và gửi từng sách tương ứng qua **responseObserver**.
- Sử dụng **CountDownLatch** để đồng bộ hóa và chờ cho đến khi tất cả các phản hồi được xử lý hoặc thời gian chờ một phút kết thúc.
- Trả về danh sách các sách, hoặc danh sách rỗng nếu hết thời gian chờ.
