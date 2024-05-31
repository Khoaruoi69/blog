---
layout: post
title: Lấy access token sử dụng refresh token với Keycloak
categories: KeyCloak
---

###  cách chúng ta lấy mới access token sử dụng refresh token với Keycloak

Đầu tiên, mình sẽ tạo mới trong Keycloak một client với Authorization Code grant type để làm ví dụ:

![_config.yml]({{ site.baseurl }}/images/keycloak-access-01.png)

Call như bài tạo Authorization Code grant type rồi call CURL mẫu như sau:

```java
curl --location 'http://localhost:8180/realms/RealmTest01/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=client_id_get_access_token_use_refresh_token' \
--data-urlencode 'client_secret=2NiKyKG986e0cA2AKy9S6dZYYUz4IGkS' \
--data-urlencode 'redirect_uri=https://oidcdebugger.com/debug' \
--data-urlencode 'grant_type=authorization_code' \
--data-urlencode 'code=4586e7dc-5741-4b52-8273-d9fa717dc98a.1d2f0cb1-cc53-43fb-bedc-7358303cadd7.adc32277-fd51-4794-87f9-8bd5b74460e3' \
--data-urlencode 'session_state=1d2f0cb1-cc53-43fb-bedc-7358303cadd7' \
--data-urlencode 'state=pye4ltv39b' \
--data-urlencode 'iss=http://localhost:8180/realms/RealmTest01'
```

Thực hiện lấy access token cho client này, các bạn sẽ thấy kết quả như sau:


![_config.yml]({{ site.baseurl }}/images/keycloak-access-02.png)

- Nội dung của refresh token nếu mình decode nó sử dụng https://jwt.io/ cơ bản như sau:

![_config.yml]({{ site.baseurl }}/images/keycloak-access-03.png)

- Các bạn để ý đến claim “typ” trong nội dung của refresh token trên nhé! Ở đây giá trị của claim này là Refresh, chúng ta còn có một số type khác của refresh token nữa, chẳng hạn như Offline,…

- Refresh token sẽ luôn được trả về cùng với access token, giúp chúng ta có thể lấy access token mới mà không cần user phải đăng nhập trở lại.

- Để lấy access token mới với refresh token thì trong request để lấy access token, các bạn chỉ cần truyền grant_type=refresh_token, giá trị của refresh token mà chúng ta có trong request lấy access token trước, client ID và client secret

- Với ví dụ này của mình, các bạn có thể lấy access token mới bằng cách request như sau:

![_config.yml]({{ site.baseurl }}/images/keycloak-access-04.png)


```java
curl --location 'http://localhost:8180/realms/RealmTest01/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=client_id_get_access_token_use_refresh_token' \
--data-urlencode 'client_secret=2NiKyKG986e0cA2AKy9S6dZYYUz4IGkS' \
--data-urlencode 'refresh_token=eyJhbGciOiJIUzUxMiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJhZGNlZWVjNi1mMGYzLTQxZDctODI3Ni0wOTg3MjAxNWRmMjMifQ.eyJleHAiOjE3MTcxNTI5MzQsImlhdCI6MTcxNzE1MTEzNCwianRpIjoiYjlkMGZmYTktYWFiMC00YTc3LThjYjMtZjhhMWMzMzc1MTRhIiwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo4MTgwL3JlYWxtcy9SZWFsbVRlc3QwMSIsImF1ZCI6Imh0dHA6Ly9sb2NhbGhvc3Q6ODE4MC9yZWFsbXMvUmVhbG1UZXN0MDEiLCJzdWIiOiI0YTE5NjgxMS0xN2YwLTRiZGYtYWY1ZC00NWE0OTgxNzMyYmYiLCJ0eXAiOiJSZWZyZXNoIiwiYXpwIjoiY2xpZW50X2lkX2dldF9hY2Nlc3NfdG9rZW5fdXNlX3JlZnJlc2hfdG9rZW4iLCJub25jZSI6IjNuOWcybWJkeHFpIiwic2Vzc2lvbl9zdGF0ZSI6IjFkMmYwY2IxLWNjNTMtNDNmYi1iZWRjLTczNTgzMDNjYWRkNyIsInNjb3BlIjoib3BlbmlkIHByb2ZpbGUgZW1haWwiLCJzaWQiOiIxZDJmMGNiMS1jYzUzLTQzZmItYmVkYy03MzU4MzAzY2FkZDcifQ.NAIzINQxbZT29fCtEgnBrE3aeRIjsyW9Cw7C5NShw5kPR_DeU_zHLyYkKerscCaQrOv6-79bj5wO7Q8QQ7Gvlw' \
--data-urlencode 'grant_type=refresh_token'
```