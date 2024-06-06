---
layout: post
title: Login Theme Customization
categories: KeyCloak
---

### Things need to change


link: https://www.youtube.com/watch?v=-0Ihefm2FyE&list=PLuxh5fnYpxkHmkhbin-kN33AcR59nmmbp&index=2

Git: https://github.com/iotstation/Keyclock-Theme-V.24/tree/main

#### Note: Chỉnh sửa xong thì build file jar bỏ vào providers: ( bằng lệnh maven: mvn package)
- Trong file pom.xml

![_config.yml]({{ site.baseurl }}/images/custom-login-01.png)

- theme --- login pages

![_config.yml]({{ site.baseurl }}/images/custom-login-02.png)

 đã thêm locales

![_config.yml]({{ site.baseurl }}/images/custom-login-03.png)

- thêm hình ảnh vào login page

![_config.yml]({{ site.baseurl }}/images/custom-login-04.png)

- muốn sửa thì vào login.css


![_config.yml]({{ site.baseurl }}/images/custom-login-05.png)

- muốn thay đổi các chữ sau thì vào **messages_en.properties**

![_config.yml]({{ site.baseurl }}/images/custom-login-06.png)

![_config.yml]({{ site.baseurl }}/images/custom-login-07.png)
