---
layout: post
title: Mockito - Annotations
categories: Unit-test
---

### Một số Annotation của Mockito

#### **@Mock**

- Annotation @Mock được sử dụng để khởi tạo một mock object và inject giá trị này cho field này. Chúng ta không tạo ra các đối tượng thực sự, thay vào đó yêu cầu Mockito tạo ra một đối tượng giả cho class này, các phương thức của class này không được thực thi thực sự, do đó trạng thái của đối tượng không bị thay đổi.

- Ngoài cách sử dụng @Mock trên mức field, chúng ta có thể sử dụng phương thức Mockito.mock(), nó cho cùng kết quả với @Mock. Cách sử dụng Annotation ngắn hơn, dễ hiểu hơn hơn và thường được sử dụng hơn

- Thay đổi default value của mock object: 
 
 + **Mockito.mock(Class<T> classToMock, Answer defaultAnswer) :** cho phép tạo một mock object với loại Answer được chỉ định. Loại Answer chỉ định cách xử lý mặc định nếu một stub method không được xác định.

+ **Mockito.mock(Class<T> classToMock, MockSettings mockSettings) :** cho phép tạo một mock object vói các setting được chỉ định. Các setting này có thể là defaultAnswer(), serializable(), name(), …

- Các **Answer** được hỗ trợ bởi Mockito:

+ **RETURNS_DEFAULTS :** đây là default Answer nếu không được chỉ định. Nó trả về các giá trị “empty”, tức là null, 0, false, empty collection.

+ **RETURNS_SMART_NULLS :** tránh return null có thể gây ra lỗi NullPointerException. Nó trả về một SmartNull object. Phương thức test với object này vẫn bị fail, nhưng chúng ta có thể stack trace một unstubbed method chưa được gọi.

+ **RETURNS_MOCKS :** Mockito cố gắng return các empty value trước, sau đó đến mock object, cuối cùng sẽ return null. Chẳng hạn, đối với các String hoặc array, nếu nó không được stub thì Mockito sẽ trả về null. Nếu sử dụng loại Answer này, kết quả trả về là một emty string, empty array.

+ **CALLS_REAL_METHODS :** gọi một real method nếu các method không được stub, tương tự như sử dụng @Spy.

*[Tài liệu Mockito ](https://www.javadoc.io/doc/org.mockito/mockito-core/2.24.5/org/mockito/Mockito.html#46)*

### @Spy

- Annotation @Spy được sử dụng để wrap một object thật, có thể gọi xử lý thật sự ở object này, tuy nhiên chúng ta có thể spy một số phương thức trên đối tượng thật như với @Mock.

- Ngoài cách sử dụng @Spy, chúng ta có thể sử dụng phương thức Mockito.spy(), nó cho cùng kết quả với @Spy.

### @InjectMocks

- Trong một số trường hợp, chúng ta cần tạo một object test mà object này chứa các dependency khác. Vì vậy, chúng ta cần phải tạo các Mock/ Spy object cho các dependency và inject chúng vào đối tượng test. Để làm được điều này, chúng ta có thể sử dụng Annotation @InjectMocks.

- @InjectMocks được sử dụng ở mức field, để đánh dấu các field này cần inject các dependency. Mockito cố gắng inject các giá trị cho các field này thông qua constructor, setter hoặc property injection. Nó sẽ không throw bất kỳ lỗi nào nếu không tìm được injection phù hợp.

- Khả năng của @InjectMocks:

+ Tạo một real instance object để test.

+ Có thể gọi thực thi thực sự các phương thức được test.

+ Inject các dependency đã được khởi tạo bằng mock object (@Mock) vào object test.

- Sự khác nhau giữa @Mock và @InjectMocks:

+ @Mock được sử dụng để tạo một mock object.

+ @InjectMocks được sử dụng để khởi tạo một real object và inject các dependency.

### Captor

- Annotation @Captor được sử dụng để tạo một instance cho ArgumentCaptor. Lớp ArgumentCaptor cho phép truy cập các đối số của các phương thức được gọi trong quá trình verify. Vì vậy chúng ta có thể lấy được các đối số này và sử dụng chúng để test.