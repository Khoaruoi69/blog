---
layout: post
title: Throw và Throws trong java
categories: Exception-Handling
---

#### 1. Từ khóa throw trong java

Từ khoá throw trong java được sử dụng để ném ra một ngoại lệ (exception) cụ thể.

Chúng ta có thể ném một trong hai ngoại lệ **checked hoặc unchecked** trong java bằng từ khóa throw. Từ khóa throw chủ yếu được sử dụng để ném ngoại lệ tùy chỉnh (ngoại lệ do người dùng tự định nghĩa).

Cú pháp:

    - throw exception;

*Ví dụ:* tạo ra phương thức validate() với tham số truyền vào là giá trị integer. Nếu tuổi dưới 18, chúng ta ném ra ngoại lệ ArithmeticException nếu không in ra một thông báo “welcome”.

``` java 
public class ThrowExample {
 
    static void validate(int age) {
        if (age < 18)
            throw new ArithmeticException("not valid");
        else
            System.out.println("welcome to vote");
    }
 
    public static void main(String args[]) {
        validate(13);
        System.out.println("rest of the code...");
    }
 
}
```

Kết quả thực thi chương trình trên: 

    - Exception in thread "main" java.lang.ArithmeticException: not valid
      at com.gpcoder.throwex.ThrowExample.validate(ThrowExample.java:7)
      at com.gpcoder.throwex.ThrowExample.main(ThrowExample.java:13)

### 2. Quá trình lan truyền Exception trong Java

#### 2.1 Method Call Stack

Trước khi tìm hiểu về lan truyền Exception trong java, chúng ta hãy tìm hiểu Method Call back trong Java.

Một ứng dụng bao gồm nhiều cấp (level) của phương thức, chúng được quản lý bởi một cái gọi là ngăn xếp gọi phương thức **(Method Call Stack)**. Ngăn xếp (Stack) là một danh sách mà ta giới hạn việc thêm vào hoặc loại bỏ một phần tử chỉ thực hiện tại một đầu của danh sách, đầu này gọi là đỉnh (TOP) của ngăn xếp.

Trong ví dụ sau, phương thức main() gọi methodA(); methodA() gọi methodB(); methodB() gọi methodC().

``` java 
public class MethodCallStackDemo {
    public static void main(String[] args) {
        System.out.println("Enter main()");
        methodA();
        System.out.println("Exit main()");
    }
 
    public static void methodA() {
        System.out.println("Enter methodA()");
        methodB();
        System.out.println("Exit methodA()");
    }
 
    public static void methodB() {
        System.out.println("Enter methodB()");
        methodC();
        System.out.println("Exit methodB()");
    }
 
    public static void methodC() {
        System.out.println("Enter methodC()");
        System.out.println("Exit methodC()");
    }
}
```

Kết quả thực thi: 

    - Enter main()
      Enter methodA()
      Enter methodB()
      Enter methodC()
      Exit methodC()
      Exit methodB()
      Exit methodA()  
      Exit main()

![_config.yml]({{ site.baseurl }}/images/stack-method.png)


#### 2.2 Lan truyền ngoại lệ (exception propagation)

Một ngoại lệ đầu tiên được ném ra từ phía trên của call stack ( stack chứa các phương thức gọi đến nhau) và nếu nó không được catch, nó sẽ giảm xuống ngăn xếp đến phương thức trước, nếu không được catch ở đó, ngoại lệ lại giảm xuống phương thức trước, và cứ như vậy cho đến khi chúng được catch hoặc cho đến khi chúng chạm đến đáy của stack.
Điều này được gọi là truyền ngoại lệ ( exception propagation).

![_config.yml]({{ site.baseurl }}/images/call-stack.png)

#### 2.3 Unchecked Exception được lan truyền trong Calling Chain

``` java 
class TestExceptionPropagation {
    void m() {
        int data = 50 / 0;
    }
 
    void n() {
        m();
    }
 
    void p() {
        try {
            n();
        } catch (Exception e) {
            System.out.println("exception handled");
        }
    }
 
    public static void main(String args[]) {
        TestExceptionPropagation obj = new TestExceptionPropagation();
        obj.p();
        System.out.println("normal flow...");
    }
}
```

Kết quả thực thi: 

    - exception handled
      normal flow...

Trong ví dụ trên, ngoại lệ ArithmeticException (đây là Unchecked Exception) xảy ra trong phương thức m() nơi nó không được xử lý, do đó nó được truyền đến phương thức n() nhưng nó không được xử lý, một lần nữa nó được truyền đến phương thức p() trong đó ngoại lệ được xử lý.

Ngoại lệ có thể được xử lý trong bất kỳ phương thức nào trong call stack hoặc trong phương thức main(), phương thức p(), phương thức n() hoặc m().

#### 2.4 Checked Exception không được lan truyền trong Calling Chain

``` java
class TestExceptionPropagation {
    void m() {
        throw new java.io.IOException("device error"); // checked exception
    }
 
    void n() {
        m();
    }
 
    void p() {
        try {
            n();
        } catch (Exception e) {
            System.out.println("exception handeled");
        }
    }
 
    public static void main(String args[]) {
        TestExceptionPropagation2 obj = new TestExceptionPropagation2();
        obj.p();
        System.out.println("normal flow");
    }
}
```

Thực thi chương trình:

    - Compile Time Error

### 3. Từ khóa throws trong java

Từ khóa throws trong java được sử dụng để khai báo một ngoại lệ. Nó thể hiện thông tin cho lập trình viên rằng có thể xảy ra một ngoại lệ, vì vậy nó là tốt hơn cho các lập trình viên để cung cấp các mã xử lý ngoại lệ để duy trì luồng bình thường của chương trình.

Exception Handling chủ yếu được sử dụng để xử lý ngoại lệ checked. Nếu xảy ra bất kỳ ngoại lệ unchecked như NullPointerException, đó là lỗi của lập trình viên mà anh ta không thực hiện kiểm tra trước khi code được sử dụng.

**Cú pháp:**

    - return_type method_name() throws exception_class_name {
        // method code
        }

**Ngoại lệ nào nên được khai báo**

Chỉ ngoại lệ **Checked**, bởi vì:

+ Ngoại lệ **unchecked** nằm trong tầm kiểm soát của bạn

+ **Error:** nằm ngoài tầm kiểm soát của bạn, ví dụ bạn ko thể làm đc bất cứ điều gì khi các lỗi VirtualMachineError or StackOverFllowError xảy ra.

**Lợi ích của throws trong java**

+ Ngoại lệ checked có thể được ném ra ngoài và được xử lý ở một hàm khác.

+ Cung cấp thông tin cho caller của phương thức về các ngoại lệ. 

### 3. Sự khác nhau giữa throw và throws trong java

![_config.yml]({{ site.baseurl }}/images/compare-throw-vs-throws.png)

**Tài liệu tham khảo:**

*[Từ khóa Throw và Throws trong java](https://gpcoder.com/2447-tu-khoa-throw-va-throws-trong-java/)*