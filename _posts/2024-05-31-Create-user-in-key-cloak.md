---
layout: post
title:  Tạo mới user trong Keycloak
categories: KeyCloak
---

### Tạo mới user trong Keycloak

- Để sử dụng các application mà chúng sử dụng Keycloak cho authentication và authorization, chúng ta cần có user để đăng nhập

- Thông thường ở các hệ thống lớn thì thông tin user sẽ được lưu trữ trong Active Directory (AD) hoặc LDAP, Keycloak có thể kết nối tới các hệ thống này để lấy thông tin user

- Nó cũng có thể lưu trữ thông tin user trực tiếp luôn. Trong bài viết này, mình sẽ hướng dẫn các bạn cách tạo mới user trong Keycloak!

- Đầu tiên, các bạn cần login vào Keycloak bằng tài khoản admin, sau đó thì chọn menu item Users ở bên trái. Nội dung trang Users sẽ hiển thị như sau:

![_config.yml]({{ site.baseurl }}/images/keycloak-21.png)

![_config.yml]({{ site.baseurl }}/images/keycloak-22.png)

- Username là tên user dùng để đăng nhập và đây là thông tin bắt buộc chúng ta phải nhập nhé các bạn!

- Email là email của user.

- Email Verified thì cho phép chúng ta xác nhận là email của user đã xác nhận hay chưa? ON là đã xác nhận còn OFF là chưa nha các bạn!

- First Name và Last Name là tên của user.

- Các bạn có thể enable hoặc disable user sử dụng ô Enabled với giá trị ON hoặc OFF.

- Required user actions định nghĩa một số thao tác:

![_config.yml]({{ site.baseurl }}/images/keycloak-23.png)

- mà nếu các bạn muốn thì có thể chọn để user làm.

- Sau khi điền đầy đủ thông tin, các bạn có thể nhấn nút Create để hoàn tất việc tạo mới.

- Ví dụ của mình như sau:

![_config.yml]({{ site.baseurl }}/images/keycloak-24.png)

- Kết quả:

![_config.yml]({{ site.baseurl }}/images/keycloak-25.png)

- Để cập nhập thông tin mật khẩu đăng nhập, các bạn hãy đi đến tab Credentials trong cửa sổ này rồi nhấn nút Set password:

![_config.yml]({{ site.baseurl }}/images/keycloak-26.png)

- Các bạn hãy nhập Password và Password Confirmation.

- Nếu đây chỉ là password tạm thời, user cần thay đổi password khi login thì các bạn hãy để ô Temporary mặc định với giá trị ON, ngược lại thì hãy OFF nó.

![_config.yml]({{ site.baseurl }}/images/keycloak-27.png)

- Cuối cùng thì các bạn hãy nhấn nút Save để lưu lại mật khẩu này. Cửa sổ sau sẽ xuất hiện:

![_config.yml]({{ site.baseurl }}/images/keycloak-28.png)

- để xác nhận rằng bạn thật sự muốn đặt mật khẩu cho user này.

- Tiếp tục nhấn nút Save password để xác nhận các bạn nhé!

- Kết quả:

![_config.yml]({{ site.baseurl }}/images/keycloak-29.png)

- Đến đây thì các bạn có thể sử dụng user này để đăng nhập vào Keycloak server sử dụng URL http://localhost:8080/realms/huongdanjava/account/#/ 
- với huongdanjava là tên realms mà mình đang sử dụng. Các bạn sẽ thấy kết quả như sau:
- Nhấn nút Sign In, sau đó nhập thông tin user mà các bạn vừa mới tạo. Nếu password mà các bạn vừa mới set là password tạm, các bạn sẽ thấy Keycloak yêu cầu change password như sau:

![_config.yml]({{ site.baseurl }}/images/keycloak-31.png)

- Sau khi nhập password mới, các bạn sẽ thấy kết quả như sau: 
 
![_config.yml]({{ site.baseurl }}/images/keycloak-30.png)