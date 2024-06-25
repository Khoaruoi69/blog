---
layout: post
title: Thêm mới client Keycloak
categories: KeyCloak
---

### Thêm mới client

link: https://huongdanjava.com/vi/them-moi-client-trong-keycloak.html

Client trong keycloak là những ứng dụng tương tác với nó để authentication và authorization. Việc thêm mới client trong keycloak là để nó quản lý tất cả những client sẽ connect tới nó, theo giao thức, chuẩn authentication và authorization nào. Trong bài viết này, sẽ hướng dẫn các bạn cách thêm mới 1 client trong keycloak với các thông tin cơ bản.

Đầu tiên, các bạn cần đăng nhập vào realm mà các bạn muốn thêm mới client, của mình như sau:

![_config.yml]({{ site.baseurl }}/images/cl-01.png)

sau đó thì ở menu bên trái nhấn vào item clients:

![_config.yml]({{ site.baseurl }}/images/cl-02.png)

Để thêm mới một client, các bạn hãy nhấn vào nút Create client trong trang này. Một trang Create Client General Settings sẽ hiển thị như sau:

![_config.yml]({{ site.baseurl }}/images/cl-03.png)

Các bạn hãy điền thông tin client sử dụng các field Client type, Client ID, Name và Description.

Ở field Client type, chúng ta có 2 type mà Keycloak hỗ trợ là **OpenID Connect (một extension của chuẩn OAuth)** và **SAML (Security Assertion Markup Language)**, tuỳ theo mục đích của mình mà các bạn chọn cho hợp lý nhé!

- Giả sử bây giờ mình có một ứng dụng Angular tên là angular-test, mình muốn sử dụng Keycloak để quản lý việc authentication và authorization theo chuẩn OAuth2. Cho Angular application, chúng ta sẽ sử dụng **grant type Authorization Code với PKCE của OAuth2.**

Để khai báo client cho ứng dụng Angular này vào Keycloak, mình sẽ điền thông tin của nó như sau:

![_config.yml]({{ site.baseurl }}/images/cl-04.png)

Tuỳ theo type mà các bạn sử dụng cho ứng dụng của mình, chúng ta có thể cần phải khai báo thêm những thông tin cần thiết liên quan đến type đó. Đối với type OpenID Connect, chúng ta sẽ cần khai báo thêm thông tin, sử dụng nút Next.

Nhấn nút Next, các bạn sẽ thấy Keycloak hiển thị trang Capability config như sau:

![_config.yml]({{ site.baseurl }}/images/cl-05.png)

- Mặc định thì đối với **type OpenID Connect**, Keycloak sẽ tạo mới một client hỗ trợ **Authorization Code** và **Resource Owner Password Credentials grant type** của OAuth2 nên các bạn thấy **field Standard flow** và **Direct access grants** được chọn.

- Keycloak còn hỗ trợ Implicit Flow (field Implicit flow),  Client Credentials (field Service accounts roles) và Device Authorization (field OAuth 2.0 Device Authorization Grant) của OAuth 2, CIBA flow (field OIDC CIBA Grant) một chuẩn extend OpenID Connect

-  Click chọn nếu bạn muốn client của mình hỗ trợ những grant type này nhé!

- **Mình sẽ không sử dụng Resource Owner Password Credentials grant type** cho Angular application của mình nên mình sẽ bỏ chọn grant type này đi!

- Ở đây, các bạn còn có thể thấy 2 field Client authentication và Authorization đang được turn off.
- **Client authentication field** liên quan đến **access type** của client, **mặc định “off”** có nghĩa là access type là **public**, **“on”** là **confidential** nha các bạn! Liên quan đến access type của client các bạn có thể đọc thêm ở đây.

- **Authorization field** thì liên quan đến việc hỗ trợ **authorization**, định nghĩa **scope**, **authorization policies**, cho client của chúng ta!

- **Cho Angular application**, **access type** nên là **public** và không hỗ trợ **Authorization** nên mình sẽ **không turn on 2 field** này các bạn nhé!

Nhấn nút Save, các bạn sẽ thấy client được tạo với kết quả như sau:

![_config.yml]({{ site.baseurl }}/images/cl-06.png)

Chúng ta có thể enable hay disable client này bằng field Enabled và có rất nhiều field mà chúng ta có thể cấu hình cho thông tin của một client.

- **Cho Angular application**, trong phần Access settings, các bạn nên cấu hình field **Valid redirect URIs**. Ở local, giá trị của field này có thể là: http://localhost:4200/* như sau:

![_config.yml]({{ site.baseurl }}/images/cl-07.png)

Chúng ta cũng có thể thay đổi theme cho trang login của user bằng field Login Theme trong phần Login Settings.

- Trong tab Advanced, phần Advanced Settings, các bạn có thể cấu hình Code Exchange method của grant type Authorization Code với PKCE nếu muốn:

![_config.yml]({{ site.baseurl }}/images/cl-08.png)

Mặc định nếu chúng ta không cấu hình cho field này thì Keycloak sẽ không apply PKCE cho client này, trừ khi client gửi code exchange và code challenge method.

Sau khi cấu hình xong thông tin cho Client thì các bạn hãy click nút Save để lưu lại những cấu hình đó nhé!

Trong bài viết này, mình chỉ đề cập những thông tin cấu hình cơ bản của một Client được tạo trong Keycloak sử dụng grant type Authorization Code với PKCE. Tuỳ theo grant type mà client hỗ trợ thì còn nhiều thông tin, cấu hình khác nữa. Tuỳ nhu cầu, các bạn hãy tìm hiểu thêm nhé!