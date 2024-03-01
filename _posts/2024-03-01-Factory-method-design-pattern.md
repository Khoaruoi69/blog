---
layout: post
title: Factory Method - Design Pattern
categories: Design-pattern creational-design-pattern
---

### 1. Factory Method Pattern là gì?

**Factory Method Design Pattern** hay gọi ngắn gọn là **Factory Pattern** là một trong những **Pattern** thuộc nhóm **Creational Design Pattern**. Nhiệm vụ của **Factory Pattern** là quản lý và trả về các đối tượng theo yêu cầu, giúp cho việc khởi tạo đổi tượng một cách linh hoạt hơn.

**Factory Pattern** đúng nghĩa là một nhà máy, và nhà máy này sẽ **“sản xuất”** các đối tượng theo yêu cầu của chúng ta.

Trong **Factory Pattern**, chúng ta tạo đối tượng mà không để lộ logic tạo đối tượng ở phía người dùng và tham chiếu đến đối tượng mới được tạo ra bằng cách sử dụng một interface chung.

**Factory Pattern** được sử dụng khi có một class cha (super-class) với nhiều class con (sub-class), dựa trên đầu vào và phải trả về 1 trong những class con đó.

### 2. Triển khai Factory như thế nào ?

Một Factory Pattern bao gồm các thành phần cơ bản sau:

+ **Super Class:** môt supper class trong Factory Pattern có thể là một **interface, abstract class** hay một **class thông thường**.

+ **Sub Classes:** các sub class sẽ **implement** các phương thức của **supper class** theo nghiệp vụ riêng của nó.

+ **Factory Class:** một class chịu tránh nhiệm khởi tạo các đối tượng sub class dựa theo tham số đầu vào.
*Lưu ý: lớp này là **Singleton** hoặc cung cấp một **public static method** cho việc truy xuất và khởi tạo đối tượng. Factory class sử dụng if-else hoặc switch-case để xác định class con đầu ra.*

**Ví dụ:**

 Tất cả hệ thống ngân hàng có cung cấp API để truy cập đến hệ thống của họ. Team được giao nhiệm vụ thiết kế một API để client có thể sử dụng dịch vụ của một ngân hàng bất kỳ. Hiện tại, phía client chỉ cần sử dụng dịch vụ của 2 ngân hàng là VietcomBank và TPBank. Tuy nhiên để dễ mở rộng sau này, và phía client mong muốn không cần phải thay đổi code của họ khi cần sử dụng thêm dịch vụ của ngân hàng khác. Với yêu cầu như vậy, chúng ta có thể sử dụng một Pattern phù hợp là Factory Method Pattern.

![_config.yml]({{ site.baseurl }}/images/system-bank-factory-method.png)

Chương trình được cài đặt theo Factory Pattern như sau:

**Super class:**

```java
public interface Bank {
    String getBankName();
}
```

**Sub Classes:**

+ Class TBank

```java
package com.gpcoder.patterns.creational.factorymethod;
 
public class TPBank implements Bank {
 
    @Override
    public String getBankName() {
        return "TPBank";
    }
 
}
```

+ class VietComnBank

```java
package com.gpcoder.patterns.creational.factorymethod;
 
public class VietcomBank implements Bank {
 
    @Override
    public String getBankName() {
        return "VietcomBank";
    }
 
}
```

**Factory class:**

```java
public class BankFactory {
 
    private BankFactory() {
    }
 
    public static final Bank getBank(BankType bankType) {
        switch (bankType) {
 
        case TPBANK:
            return new TPBank();
 
        case VIETCOMBANK:
            return new VietcomBank();
 
        default:
            throw new IllegalArgumentException("This bank type is unsupported");
        }
    }
 
}
```

**Bank type:**

```java
public enum BankType {
 
    VIETCOMBANK, TPBANK;
 
}
```

**Client:**

```java
public class Client {
 
    public static void main(String[] args) {
        Bank bank = BankFactory.getBank(BankType.TPBANK);
        System.out.println(bank.getBankName()); // TPBank
    }
}
```

Như bạn thấy, phía client chỉ cần gọi duy nhất một phương thức BankFactory.getBank() là có thể sử dụng được dịch vụ của một ngân hàng bất kỳ.

Khi hệ thống muốn cung cấp thêm dịch vụ của một ngân hàng khác, chẳng hạn VietinBank, thì cần tạo thêm một class mới implement từ interface Bank, và thêm vào logic khởi tạo Bank trong Factory là xong. Nó không làm ảnh hưởng đến code ở phía Client.

#### 3. Sử dụng Factory Pattern khi nào ?

Factory Pattern được sử dụng khi:

+ Chúng ta có 1 super class với nhiều class con và dựa trên đầu vào, chúng ta cần trả về một class con. Mô hình này giúp chúng ta đưa ra trách nhiệm của việc khởi tạo
một lớp từ phía người dùng (client) sang lớp Factory.

+ Chúng ta không biết sau này sẽ cần đến những lớp con nào nữa. Khi cần mở rộng, hãy tạo ra sub class và implement thêm vào factory cho việc khởi tạo sub class này.

#### 4. Lợi ích của Factory Pattern là gì ? 

**Lợi ích của Factory Pattern:**

+ Factory Pattern giúp giảm sự phụ thuộc giữa các module (loose coupling): cung cấp 1 hướng tiếp cận với Interface thay thì các implement. Giúp chuơng trình độc lập với những lớp cụ thể mà chúng ta cần tạo 1 đối tượng, code ở phía client không bị ảnh hưởng khi thay đổi logic ở factory hay sub class.

+ Mở rộng code dễ dàng hơn: khi cần mở rộng, chỉ việc tạo ra sub class và implement thêm vào factory method.

+ Khởi tạo các Objects mà che giấu đi xử lí logic của việc khởi tạo đấy. Người dùng không biết logic thực sực được khởi tạo bên dưới phương thức factory.

+ Dễ dạng quản lý life cycle của các Object được tạo bởi Factory Pattern.

+ Thống nhất về naming convention: giúp cho các developer có thể hiểu về cấu trúc source code.

**Bạn có thể thấy Factory Pattern được áp dụng trong:**

+ JDK: java.util.Calendar, ResourceBundle, NumberFormat, …
+ BeanFactory trong Spring Framework.
+ SessionFactory trong Hibernate Framework…

**Tài liệu tham khảo:**

*[Factory Method Design Pattern](https://gpcoder.com/4352-huong-dan-java-design-pattern-factory-method/)*