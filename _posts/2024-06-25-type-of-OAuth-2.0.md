---
layout: post
title: Các loại client types trong OAuth 2.0
categories: KeyCloak
---

### Các loại client types trong OAuth 2.0

- Trong OAuth 2.0, chúng ta có thể có nhiều loại Client Application khác nhau mà người dùng có thể sử dụng để access tới các resources của resource server. Ví dụ như chúng ta có native mobile application, web application bao gồm cả Single Page Application, console hay backend application, … Những Client Application này sẽ cần phải đăng ký với Authorization Server và sử dụng thông tin Client ID được cung cấp bởi Authorization Server để lấy access token. Một số grant type trong OAuth 2.0 đòi hỏi chúng ta phải cung cấp cả Client Secret, cái mà Client Application được Authorization Server cung cấp cùng với Client Id, ví dụ như grant type Client Credentials chẳng hạn.

- Không phải tất cả các Client Application đều có thể lưu trữ Client Secret một cách bảo mật. Ví dụ như Native mobile application hay Single Page Application. Vì thế, OAuth 2.0 định nghĩa 2 loại client types khác nhau bao gồm: các confidential clients và các public clients.

- Confidentials clients là những client có thể lưu trữ Client Secret bảo mật, ví dụ như chúng ta có những web application có cả backend và frontend thì Client Secret có thể lưu trữ ở backend side, không ai có thể lấy được thông tin này.

- Ngược lại thì public clients là những client không thể đảm bảo việc lưu trữ Client Secret bảo mật, ví dụ như native mobile application hay Single Page Application, người dùng có thể decode hoặc view source code để xem thông tin Client Secret. Đối với những clients này, chúng ta cần sử dụng những grant type mà không cần Client Seret ví dụ như Authorization Code grant type với PKCE các bạn nhé!