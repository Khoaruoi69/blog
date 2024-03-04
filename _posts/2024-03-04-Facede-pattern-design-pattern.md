---
layout: post
title: Facade Pattern - Design Pattern
categories: Design-pattern Structural-design-pattern
---

#### 1. Facade Pattern là gì ?

**Facade Pattern** là một trong những Pattern thuộc nhóm cấu trúc ( Structural Pattern). 
Pattern này cung cấp một giao diện chung đơn giản thay cho 1 nhóm các giao diện có trong một hệ thống con ( subsystem). Facede Pattern định nghĩa một giao diện ở một cấp
độ cao hơn để giúp cho người dùng dễ dàng sử dụng hệ thống con này.

**Pacede Pattern** cho phép các đối tượng truy cập trực tiếp giao diện chung này để giao tiếp với các giao diện trong hệ thống con.
 Mục tiêu là che giấu các hoạt động phức tạp bên trong hệ thống con, làm cho hệ thống con dễ sử dụng hơn.

 Ví dụ khi không có Facade, hệ thống của chúng ta trông giống như sau:

![_config.yml]({{ site.baseurl }}/images/no-facade.png)

Khi có Facade, nó trông giống như sau:

![_config.yml]({{ site.baseurl }}/images/have-facade.png)

**Façade Pattern** tương tự với **Adapter Pattern**. Hai Pattern này làm việc theo cùng một cách, nhưng mục đích sử dụng của chúng khác nhau. 
**Adapter Pattern** chuyển đổi mã nguồn để làm việc được với mã nguồn khác. Nhưng **Façade Pattern** cho phép bao bọc mã nguồn gốc để nó có thể giao tiếp với mã nguồn khác dễ dàng hơn.

### 2. Cài đặt Facade Pattern như thế nào ?

![_config.yml]({{ site.baseurl }}/images/facade-pattern.png)

Các thành phần cơ bản của một Facade Pattern:

+ **Facade:** biết rõ lớp của hệ thống con nào đảm nhận việc đáp ứng yêu cầu của client, sẽ chuyển yêu cầu của client đến các đối tượng của hệ thống con tương ứng.

+ **Subsystems:** cài đặt các chức năng của hệ thống con, xử lý công việc được gọi bởi Facade. Các lớp này không cần biết Facade và không tham chiếu đến nó.

+ **Client:** đối tượng sử dụng Facade để tương tác với các subsystem.

Các đối tượng **Facade** thường là **Singleton** bởi vì chỉ cần duy nhất một đối tượng Facade.

**Ví dụ:** Một công ty bán hàng online, chẳng hạn Tiki cung cấp nhiều lựa chọn cho khách hàng khi mua sản phẩm. Khi một sản phẩm được mua, nó sẽ qua các bước xử lý: lấy thông tin về tài khoản mua hàng, thanh toán, vận chuyển, gửi Email/ SMS thông báo.

Ứng dụng của chúng ta được thiết kế với Facade Pattern, bao gồm các class như sau:

+ Thông tin về tài khoản (AccountService) : lấy thông tin cơ bản của khách hàng thông qua email được cung cấp.\

+ Dịch vụ thanh toán (PaymentService) : có thể thanh toán thông qua Paypal, thẻ tín dụng (Credit Card), tài khoản ngân hàng trực tuyến (E-banking), Tiền mặt (cash).

+ Dịch vụ vận chuyển (ShippingService) : có thể chọn Free Shipping, Standard Shipping, Express Shipping.

+ Dịch vụ email (EmailService) : có thể gửi mail cho khách hàng về tình hình đặt hàng, thanh toán, vận chuyển, nhận hàng.

+ Dịch vụ tin nhắn (SMS) : có thể gửi thông báo SMS cho khách hàng khi thanh toán online.

+ ShopFacade : là một Facade Pattern, class này bao gồm các dịch vụ có bên trong hệ thống. Nó cung cấp một vài phương thức để Client có thể dễ dàng mua hàng. Tùy vào nghiệp vụ mà nó sẽ sử dụng những dịch tương ứng, chẳng hạn dịch vụ SMS chỉ được sử dụng nếu khách hàng đăng ký mua hàng thông qua hình thức thanh toán online (Paypal, E-banking, …).

+ Client : là người dùng cuối sử dụng ShopFacade để mua hàng.

![_config.yml]({{ site.baseurl }}/images/shopFacade.png)

**AccountService.java**

```java
package com.gpcoder.patterns.structural.facade;
 
public class AccountService {
 
    public void getAccount(String email) {
        System.out.println("Getting the account of " + email);
    }
}
```

**PaymentService.java**

```java
package com.gpcoder.patterns.structural.facade;
 
public class PaymentService {
 
    public void paymentByPaypal() {
        System.out.println("Payment by Paypal");
    }
 
    public void paymentByCreditCard() {
        System.out.println("Payment by Credit Card");
    }
 
    public void paymentByEbankingAccount() {
        System.out.println("Payment by E-banking account");
    }
 
    public void paymentByCash() {
        System.out.println("Payment by cash");
    }
}
```


**ShippingService.java**

```java
package com.gpcoder.patterns.structural.facade;
 
public class ShippingService {
 
    public void freeShipping() {
        System.out.println("Free Shipping");
    }
 
    public void standardShipping() {
        System.out.println("Standard Shipping");
    }
 
    public void expressShipping() {
        System.out.println("Express Shipping");
    }
}
```


**EmailService.java**

```java
package com.gpcoder.patterns.structural.facade;
 
public class EmailService {
 
    public void sendMail(String mailTo) {
        System.out.println("Sending an email to " + mailTo);
    }
}
```


**SmsService.java**

```java
package com.gpcoder.patterns.structural.facade;
 
public class SmsService {
 
    public void sendSMS(String mobilePhone) {
        System.out.println("Sending an mesage to " + mobilePhone);
    }
}
```

**ShopFacade.java**

```java
package com.gpcoder.patterns.structural.facade;
 
public class ShopFacade {
 
    private static final ShopFacade INSTANCE = new ShopFacade();
 
    private AccountService accountService;
    private PaymentService paymentService;
    private ShippingService shippingService;
    private EmailService emailService;
    private SmsService smsService;
 
    private ShopFacade() {
        accountService = new AccountService();
        paymentService = new PaymentService();
        shippingService = new ShippingService();
        emailService = new EmailService();
        smsService = new SmsService();
    }
 
    public static ShopFacade getInstance() {
        return INSTANCE;
    }
 
    public void buyProductByCashWithFreeShipping(String email) {
        accountService.getAccount(email);
        paymentService.paymentByCash();
        shippingService.freeShipping();
        emailService.sendMail(email);
        System.out.println("Done\n");
    }
 
    public void buyProductByPaypalWithStandardShipping(String email, String mobilePhone) {
        accountService.getAccount(email);
        paymentService.paymentByPaypal();
        shippingService.standardShipping();
        emailService.sendMail(email);
        smsService.sendSMS(mobilePhone);
        System.out.println("Done\n");
    }
}
```


**Client.java**

```java
package com.gpcoder.patterns.structural.facade;
 
package com.gpcoder.patterns.structural.facade;
 
public class Client {
 
    public static void main(String[] args) {
        ShopFacade.getInstance().buyProductByCashWithFreeShipping("contact@gpcoder.com");
        ShopFacade.getInstance().buyProductByPaypalWithStandardShipping("gpcodervn@gmail.com", "0988.999.999");
    }
}
```


**Output của chương trình trên:**

```java
Getting the account of contact@gpcoder.com
Payment by cash
Free Shipping
Sending an email to contact@gpcoder.com
Done
 
Getting the account of gpcodervn@gmail.com
Payment by Paypal
Standard Shipping
Sending an email to gpcodervn@gmail.com
Sending an mesage to 0988.999.999
Done
```

Như bạn thấy phía Client chỉ sử dụng một phương thức duy nhất là có thể mua được sản phẩm mặc dù bên dưới hệ thống có rất nhiều dịch vụ xử lý khác nhau. Nếu không có Facade, phía Client sẽ không biết sử dụng những dịch vụ nào để có thể mua được sản phẩm. Khi phát sinh thêm một dịch vụ sẽ rất khó khăn khi sửa đổi và code phía Client cũng sẽ bị ảnh hưởng.

### 3. Lợi ích của Facade Pattern là gì ? 

+ Giúp cho hệ thống của bạn trở nên đơn giản hơn trong việc sử dụng và trong việc hiểu nó, vì một mẫu Facade có các phương thức tiện lợi cho các tác vụ chung.

+ Giảm sự phụ thuộc của các mã code bên ngoài với hiện thực bên trong của thư viện, vì hầu hết các code đều dùng Facade, vì thế cho phép sự linh động trong phát triển các hệ thống.

+ Đóng gói tập nhiều hàm API được thiết kế không tốt bằng một hàm API đơn có thiết kế tốt hơn.

### 4. Sử dụng Facade Pattern khi nào ? 

+ Khi hệ thống có rất nhiều lớp làm người sử dụng rất khó để có thể hiểu được quy trình xử lý của chương trình. Và khi có rất nhiều hệ thống con mà mỗi hệ thống con đó lại có những giao diện riêng lẻ của nó nên rất khó cho việc sử dụng phối hợp. Khi đó có thể sử dụng Facade Pattern để **tạo ra một giao diện đơn giản cho người sử dụng một hệ thống phức tạp.**

+ Khi người sử dụng phụ thuộc nhiều vào các lớp cài đặt. Việc áp dụng Façade Pattern sẽ tách biệt hệ thống con của người dùng và các hệ thống con khác, do đó **tăng khả năng độc lập** và **khả chuyển** của hệ thống con, dễ chuyển đổi nâng cấp trong tương lai.

+ Khi bạn muốn phân lớp các hệ thống con. Dùng Façade Pattern để định nghĩa cổng giao tiếp chung cho mỗi hệ thống con, do đó giúp **giảm sự phụ thuộc** của các hệ thống con vì các hệ thống này chỉ giao tiếp với nhau thông qua các cổng giao diện chung đó.

+ Khi bạn muốn bao bọc, **che giấu tính phức tạp** trong các hệ thống con đối với phía Client.