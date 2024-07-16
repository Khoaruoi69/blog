---
layout: post
title: Two factor authentication with SMS in KeyCloak
categories: SPI
---

### Keycloak -- 2FA -- MFA -- Authentication -- java

link: https://www.n-k.de/2020/12/keycloak-2fa-sms-authentication.html

link youtobe: https://www.youtube.com/watch?v=GQi19817fFk


![_config.yml]({{ site.baseurl }}/images/sms-01.png)

![_config.yml]({{ site.baseurl }}/images/sms-02.png)


--- Copy browser (duplicate)

![_config.yml]({{ site.baseurl }}/images/sms-03.png)

--- setting 

![_config.yml]({{ site.baseurl }}/images/sms-04.png)

-- bindings

![_config.yml]({{ site.baseurl }}/images/sms-05.png)

--------------------------------------------- 

## Code

- Dựa vào phương thức requiresUser() này, bạn trả về true. Điều này ngụ ý rằng khi một hoạt động hoặc một tình huống nhất định xảy ra (cụ thể là yêu cầu của người dùng), hệ thống của bạn yêu cầu một người dùng đã đăng nhập để tiếp tục. Nếu người dùng chưa đăng nhập, họ sẽ cần phải đăng nhập trước khi thực hiện hành động tiếp theo.

![_config.yml]({{ site.baseurl }}/images/sms-06.png)

- Đoạn code bạn cung cấp là một phương thức được triển khai để kiểm tra xem một người dùng có được cấu hình cho một tính năng cụ thể không, dựa trên các thuộc tính của người dùng trong Keycloak.

*Phương thức configuredFor: Đây là một phương thức được triển khai từ một giao diện hoặc lớp cụ thể trong dự án của bạn. Nó nhận vào ba đối số:*

*KeycloakSession session: Đối tượng phiên của Keycloak, cung cấp các phương thức để tương tác với Keycloak trong quá trình thực thi.*
*RealmModel realm: Đối tượng biểu diễn realm (miền) trong Keycloak. Realm là một không gian làm việc cô lập nơi các người dùng, client và các tài nguyên khác được quản lý.*
*UserModel user: Đối tượng biểu diễn người dùng trong realm đó.*
*Lấy giá trị thuộc tính của người dùng: Đoạn code sử dụng phương thức getFirstAttribute của đối tượng user để lấy giá trị của một thuộc tính trong người dùng. Trong trường hợp này, thuộc tính được lấy là MOBILE_NUMBER_FIELD.*

*So sánh với null: Sau khi lấy giá trị thuộc tính, đoạn code kiểm tra xem giá trị này có khác null hay không.*

*Trả về kết quả: Phương thức configuredFor trả về kết quả của biểu thức so sánh user.getFirstAttribute(MOBILE_NUMBER_FIELD) != null. Nếu thuộc tính MOBILE_NUMBER_FIELD của người dùng không có giá trị (tức là null), phương thức sẽ trả về false, ngụ ý rằng người dùng không được cấu hình cho tính năng đó. Ngược lại, nếu có giá trị, phương thức sẽ trả về true, ngụ ý rằng người dùng đã được cấu hình cho tính năng đó.*

*Tóm lại, đoạn code này dùng để kiểm tra xem một người dùng trong hệ thống Keycloak có được cấu hình cho một tính năng cụ thể (trong trường hợp này là số điện thoại di động) hay không, dựa trên giá trị của thuộc tính MOBILE_NUMBER_FIELD trong thông tin người dùng.*

![_config.yml]({{ site.baseurl }}/images/sms-07.png)

-- Add attributes

![_config.yml]({{ site.baseurl }}/images/sms-08.png)

--- cấu hình cho các thuộc tính Sms-auth

![_config.yml]({{ site.baseurl }}/images/sms-09.png)

![_config.yml]({{ site.baseurl }}/images/sms-10.png)