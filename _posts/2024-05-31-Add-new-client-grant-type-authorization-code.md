---
layout: post
title:  Add new client grant type Authorization Code
categories: KeyCloak
---

### Thêm mới client hỗ trợ grant type Authorization Code của OAuth2 trong Keycloak

Mình đã giới thiệu với các bạn cách thêm mới một client trong Keycloak và cũng đã hướng dẫn các bạn cấu hình một client hỗ trợ grant type Authorization Code với PKCE của OAuth2 là như thế nào. Trong bài viết này, mình sẽ giới thiệu với các bạn cách thêm mới và cấu hình client hỗ trợ grant type Authorization Code của OAuth2 trong Keycloak các bạn nhé!

Giả sử mình đã tạo mới một client tên là huongdanjava_authorization_code trong Keycloak như sau:

![_config.yml]({{ site.baseurl }}/images/keycloak-15.png)

- Mặc định client này sẽ support Authorization Code và Resource Owner Password Credentials grant type của OAuth2 nên các bạn thấy field Standard flow và Direct access grants được chọn.

- Chúng ta sẽ disable Resource Owner Password Credentials grant type đi và turn on 2 field Client authentication và Authorization để client của chúng ta là Confidential và chúng ta có thể sử dụng access token của nó để làm authorization:

![_config.yml]({{ site.baseurl }}/images/keycloak-16.png)

- Vì trong Authorization Code grant type, sau khi user login, trang grant quyền access, consent screen, sẽ được hiển thị, các bạn có thể turn on hoặc turn off trang consent screen này bằng field Consent required trong phần Login settings. 
- Mình sẽ turn on field này để làm ví dụ:

![_config.yml]({{ site.baseurl }}/images/keycloak-17.png)

- Chúng ta có thể thay đổi theme cho trang login của user bằng field Login theme cũng trong phần Login settings này.
- Nói thêm cho các bạn biết là Keycloak hỗ trợ chúng ta custom login theme mặc định của Keycloak và nhiều thứ khác nữa, các bạn có thể tìm hiểu thêm trên mạng các bạn nhé!

- Một thông tin required khác cho **Authorization Code grant type** mà chúng ta cần cấu hình là **Redirect URIs** sử dụng field **Valid redirect URIs**. Redirect URIs này sẽ những valid URI mà Keycloak có thể sử dụng cho client này để trả về authorization code sau khi user login và grant access cho Client Application.

-  Mình sẽ sử dụng https://oidcdebugger.com/ để làm ví dụ cho bài viết này nên mình sẽ cấu hình Redirect URI là https://oidcdebugger.com/debug như sau:

![_config.yml]({{ site.baseurl }}/images/keycloak-18.png)

- Nhấn nút Save
- Thông tin client secret của client này sẽ nằm trong tab Credentials:

![_config.yml]({{ site.baseurl }}/images/keycloak-19.png)

- Đến đây thì chúng ta đã hoàn thành những cấu hình cơ bản cho client với Authorization Code grant type rồi đó các bạn!

- Để kiểm tra kết quả, đầu tiên các bạn có thể sử dụng https://oidcdebugger.com/ đóng vai trò là một Client Application để lấy authorization code với cấu hình như sau:

![_config.yml]({{ site.baseurl }}/images/keycloak-20.png)

- Authorize URI của Keycloak là http://localhost:8080/realms/RealmTest01/protocol/openid-connect/auth 

- với http://localhost:8080:  là tên server và port number mà Keycloak chúng ta đang sử dụng, đang chạy.

- RealmTest01:  là tên realm mà client của các bạn được định nghĩa.

- Chúng ta sẽ không thay đổi Redirect URI mặc định của https://oidcdebugger.com/ các bạn nhé!

- Client ID là client mà mình vừa mới tạo ở trên.
- Sau khi đã nhập những thông tin trên thì các bạn hãy nhấn nút Send Request ở dưới cùng của trang này. Trang đăng nhập thông tin user sẽ hiển thị như sau:

![_config.yml]({{ site.baseurl }}/images/keycloak-32.png)


- Yes có nghĩa là các bạn đồng ý cho Client Application truy cập những thông tin được hiển thị trong trang này, ngược lại, nếu các bạn đổi ý thì có thể nhấn nút No các bạn nhé!

- Sau khi nhấn nút Yes, authorization code sẽ được trả về cho Client Application https://oidcdebugger.com như sau:

![_config.yml]({{ site.baseurl }}/images/keycloak-33.png)

- Các bạn có thể sử dụng authorization code này cùng với client secret của client để request tới Keycloak lấy access token như sau:

CRUL:

![_config.yml]({{ site.baseurl }}/images/keycloak-34.png)