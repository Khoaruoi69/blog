---
layout: post
title: OpenID Connect
categories: KeyCloak
---

OpenID Conect (OIDC) is an uathentication protocol that is an extension of OAuth 2.0.

OAuth 2.0 is a framework for building authorization protocols and is incomplete. OIDC, howerver, is a full authentication and authorization protocol that ueses the Json Web Token (JWT) standards (*tiêu chuẩn*). The JWT standards define an identity token JSON format and methods to digitally (*bằng kỹ thuật số*) sign and encrypt data in a compact (*nhỏ gọn*) and web-friendly way. 

In general, OIDC implement two use cases (*trường hợp*). The first case is an application requesting that a Keycloak server authenticates a user (*Xác thực người dùng*).
Upon successful login, the application receives (*nhận được*)  an *indentity token* and  *Access token*. The identity token contains user information including user name, email, and profile information. The realm digitally signs the access token which contains access information (such as user role mappings) that application use determine (*quyet tam*) resources (*tai nguyen*) users can access in the application.

The second use case is a client accessing (*truy cap*) remote services:

+ The client request an *access token* form *keycloak* ton invoke (*goi*) on remote services on behalf  (*thay mat*) off the user.
+ KeyCloak authenticates the user and asks the user for consent (*bang long*) to grant access (*cap quyen truy cap*) to the requesting client.
+ The client receives (*nhan duoc*) the *access token* which is digitally signed by the realm.
+ The client makes (*thuc hien*) REST requests on remote services using the access token.
+ The remote REST service verifies the tokens signature.
+ The remote REST service decides, based on (*dua tren*) access information with the token, to process (*xu ly*) or reject (*tu choi*) the request.


### OIDC auth flows

OIDC has several methods, or flows, that clients or applications can use to authenticate users and receive  *identity* and *access tokens*. The methods depends on the type
of application or client requesting access.

#### Authorization Code Flow ( Luong ma uy quyen)

The authorization Code Flow is a browser-based protocol and suits authenticating (*phù hợp với việc xác thực*) and authorizing browser-based applications.
It uses browser redirects (*chuyển hướng*) to obtain (*co duoc*) identity and access token.

- 1. A user connects to an applications using browser. The application detects (*phat hien*) the user is not logged into the application.
- 2. The application redirects (*chuyển hướng*) the browser to keycloak for authentication.
- 3. The application passes a callback URL as a query parameter in the browser redirect. (*Ứng dụng chuyển URL gọi lại dưới dạng tham số truy vấn trong chuyển hướng trình duyệt*). Keycloak uses the parameter upon successful authentication. (*Keycloak sử dụng tham số khi xác thực thành công.*)
- 4. Keycloak authenticates the user and creates a one-time, short-lived, temporary code. (*Keycloak xác thực người dùng và tạo mã tạm thời, tồn tại trong thời gian ngắn.*)
- 5. Keycloak redirects to application using callback URL and adds the temporary code as a query parameter in the callback URL.
- 6. The application extracts (*chiết xuất*) the temporary code and makes a background REST invocation (*lời kêu gọi*) to Keycloak to exchange (*trao đổi*) the code for
an identity and access and refresh token. To prevent (*ngăn chặn*) replay attacks, the temporary code cannot be used more than once.

```java
// A systems is vulnerable to stoken token for the lifetime of that token. (*Một hệ thống dễ bị đánh cắp mã thông báo trong suốt thời gian tồn tại của mã thông báo đó.*)
// For securirty and scalability reasons (*Vì lý do bảo mật và khả năng mở rộng*), access tokens are generally (*nói chung là*) set expire quickly so subsequents (*tiep theo*) token requests fail. If a token expire, an application can obtain (*đạt được*) a new accesstoken using the additional (*thêm vào*) *refresh token* sent by the login protocol.

```

**Confidential** (*Bảo mật*) client provide client secrets when they exchange the temporary codes for token.
*Public* client are not required to provide client secrets. 
*Public* client are secure (*chắc chắn*) when HTTPS is strictly (*nghiêm ngặt*) enforced (*thi hành*) and redirect URIs (Uniform Resource Identifier)
registered for for the client are strictly controlled. HTML5/JavaScripts clients have to be *public*  client because there is no way to securely transmit the
client serect to HTML/JavaScript client .

