---
layout: post
title: Xử lý ngoại lệ trong Java ( Exception Handling )
categories: Exception-Handling
---

## Hướng dẫn xử lý ngoại lệ (Exception Handling) trong Java

**Hướng dẫn xử lý ngoại lệ (Exception Handling) trong Java, bao gồm:**

+ Hệ thống phân cấp ngoại lệ trong Java.

+ Các ngoại lệ thường gặp.

+ Phân biệt và xử lý: checked exception, unchecked exception, error.

+ Xử lý ngoại lệ với try-catch-finally.

+ Phân biệt throw, throws.

+ Tùy chỉnh ngoại lệ (Custom Exception).


### 1. Ngoại lệ (Exception) là gì  ?

Exception là một sự kiện xảy ra trong quá trình thực thi một chương trình Java, nó làm phá vỡ cái flow (luồng xử lý) bình thường của một chương trình, thậm chí chết chương trình.

Một ngoại lệ có thể xảy ra với nhiều lý do khác nhau, nó nằm ngoài dự tính của chương trình. Một vài ngoại lệ xảy ra bởi lỗi của người dùng, một số khác bởi lỗi của lập trình viên và số khác nữa đến từ lỗi của nguồn dữ liệu vật lý. Chẳng hạn như:

+ Người dùng nhập dữ liệu không hợp lệ.

+ Truy cập ngoài chỉ số mảng.

+ Một file cần được mở nhưng không thể tìm thấy.

+ Kết nối mạng bị ngắt trong quá trình thực hiện giao tiếp hoặc JVM hết bộ nhớ. ....

### 2. Hệ thống cấp bậc của các lớp ngoại lệ trong Java

Mô hình phân cấp của **Exception** trong java.
     
+ Class ở mức cao nhất là **Throwable**

+ Hai class con trực tiếp là **Error** và **Exception**

Trong nhánh **Exception** có 1 nhánh con là **RuntimeException** là các ngoại lệ sẽ không được java kiểm tra trong thời điểm biên dịch.
Ý nghĩa của được kiểm tra và không được kiểm tra tại thời điểm biên dịch sẽ được minh họa trong các ví dụ phần sau.

![_config.yml]({{ site.baseurl }}/images/exception-handling.png)

#### 2.1  Exception

Trong Java có **2 loại exception**: **checked và unchecked**. Tất cả các **checked exception** được kế thừa từ lớp **Exception** ngoại trừ lớp **RuntimeException**.
RuntimeException là lớp cơ sở của tất cả các lớp **unchecked exception**. Đó cũng là dấu hiệu để nhận biết đâu là checked exception và đâu là unchecked exception.

Điểm khác biệt giữa các lớp **checked và unchecked expcetion** chính là **thời điểm** xác định được expcetion có thể xảy ra.

**Checked exceptions**

Là loại exception xảy ra trong lúc **complie time**, nó cúng có thể được gọi là **complie time exception**. Loại exception này không thể bỏ qua được
trong quá trình complie, bắt buộc ta phải handle nó.

Các lớp extends từ lớp Throwable ngoại trừ RuntimeException và Error được gọi là checked exception.

*ví dụ:* IOException, FileNotFoundException, NoSuchFieldException, ….

![_config.yml]({{ site.baseurl }}/images/example-checked-exception.png)

**Unchecked exceptions**

Là loại exception xảy ra tại thời điểm thực thi chương trình, nó cũng có thể gọi là runtime exceptions đó là programming bugs, lỗi logic của chương trình…
Loại exception này được bỏ qua trong quá trình compile, không bắt buộc ta phải handle nó.

Các lớp extends từ RuntimeException được gọi là unchecked exception.

*Ví dụ:* NullPointerException, NumberFormatException, ArrayIndexOutOfBoundsException, DivideByZeroException,…

![_config.yml]({{ site.baseurl }}/images/example-unchecked-exception.png)

### 2.2 Error

Error là những vấn đề nghiêm trọng liên quan đến môi trường thực thi của ứng dụng hoặc hệ thống mà lập trình viên không thể kiểm soát. Nó thường làm chết chương trình.

Lớp Error định nghĩa các ngoại lệ mà không thể bắt (catch) từ chương trình.

Ví dụ: OutOfMemoryError, VirtualMachineError, and StackOverflowError, …

![_config.yml]({{ site.baseurl }}/images/example-error.png)

### 3 Xử lý ngoại lệ ( Exception Handling ) trong java

#### 3.1 Xử lý ngoại lệ là gì ?

Xử lý ngoại lệ **(Exception Handling)** trong java là một cơ chế xử lý các lỗi **runtime** để có thể duy trì luồng bình thường của ứng dụng.

Quá trình **xử lý exception** được gọi là **catch exception**, nếu **Runtime System** không xử lý được ngoại lệ thì chương trình sẽ kết thúc.

![_config.yml]({{ site.baseurl }}/images/exception.png)

#### 3.2 JVM xử lý Exception thế nào ?

Khi một lỗi xảy ra trên một method, method đó sẽ tạo ra một object và đưa nó vào **Runtime System**. Object đó được gọi là **Exception Object**, nó chứa tất cả các thông tin về lỗi và trạng thái của chương trình khi xảy ra lỗi.

Sau đó, Runtime System sẽ xử lý sẽ tìm cách xử lý ngoại lệ phù hợp được sử dụng tại method ấy. Nếu không có thì JVM tiếp tục tìm xử lý ngoại lệ phù hợp ở các method trên (là method gọi lớp hiện tại). Nếu không có method nào có xử lý ngoại lệ phù hợp thì Thread mà đang thực hiện chuỗi method xảy ra ngoại lệ bị ngắt. Nếu thread ấy là thread main thì chết chương trình.

![_config.yml]({{ site.baseurl }}/images/jvm-hand-exception.png)

#### 3.3 Các phương thức của lớp Exception

![_config.yml]({{ site.baseurl }}/images/method-exception.png)


**Tài liệu tham khảo:**

*[Xử lý ngoại lệ trong Java (Exception Handling)](https://gpcoder.com/2430-xu-ly-ngoai-le-trong-java-exception-handling/#He_thong_cap_bac_cua_cac_lop_ngoai_le_trong_Java)*