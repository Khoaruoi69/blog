---
layout: post
title: Proof Key for Code Exchange (PKCE)
categories: KeyCloak
---

### Authorization Code grant type với (PKCE)

link: https://huongdanjava.com/vi/authorization-code-grant-type-voi-proof-key-for-code-exchange-pkce-trong-oauth-2-1.html


**Authorization Code grant type với Proof Key for Code Exchange (PKCE) trong OAuth 2.1**

- Các **public client** như **Native mobile application** or **Single Page Application** không thể lưu trữ **Client Secret** trong **Authorization Code grant type** một cách bảo mật được.

- **Decode source** của **mobile application** hay **view source code** của **Single Page Application**, chúng ta có thể xem được thông tin **Client Secret** này.
Vì thế OAuth giới thiệu **Proof Key for Code Exchange (PKCE) extension** hỗ trợ cho **Authorization Code grant type** để giải quyết vấn đề này. Cụ thể như thế nào? Trong bài viết này, chúng ta sẽ cùng nhau tìm hiểu về Authorization Code grant type với Proof Key for Code Exchange (PKCE) trong OAuth 2.1 các bạn nhé!

- Ý tưởng của PKCE là sử dụng 1 cặp **secret code** bao gồm **Code Challenge và Code Verifier**, được generate bởi ứng dụng thứ ba hay còn gọi là **Client Application**

-  **Client Application** sẽ gửi **Code Challenge** đi cùng với request để lấy **authorization code**

-  Authorization server sẽ lưu Code Challenge này lại.

- Trong request get access token, **Client Application** sẽ gửi **Code Verifier**, authorization server sẽ **verify Code Challenge** và **Code Verifier** để issue access token.

Workflow của Authorization Code grant type với PKCE, mình có thể diễn đạt như sau:

![_config.yml]({{ site.baseurl }}/images/pkce-01.png)

Như các bạn thấy, Client Secret đã không còn được sử dụng trong grant type Authorization Code với PKCE nữa!
