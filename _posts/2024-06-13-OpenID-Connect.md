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


