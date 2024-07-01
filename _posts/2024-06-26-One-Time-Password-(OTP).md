---
layout: post
title: One Time Password (OTP) policies
categories: KeyCloak
---

### One Time Password (OTP)

link: https://docs.redhat.com/en/documentation/red_hat_build_of_keycloak/22.0/html/server_administration_guide/configuring-authentication_server_administration_guide#one_time_password_otp_policies


- Bản dựng Red Hat của Keycloak có một số chính sách để thiết lập trình tạo Mật khẩu một lần FreeOTP hoặc Google Authenticator.

![_config.yml]({{ site.baseurl }}/images/otp-01.png)

Red Hat build of Keycloak generates a QR code on the OTP set-up page, based on information configured in the OTP Policy tab. FreeOTP and Google Authenticator scan the QR code when configuring OTP.

###  Time-based or counter-based one time passwords

Các thuật toán có sẵn trong Red Hat build của Keycloak cho OTP generators dựa trên time-based and counter-based.

- Với Time Base One Time Passwords (TOTP), the token generator will hash the current time and a shared secrect. Máy chủ xác thực OTP bằng cách so sánh giá trị băm trong một khoảng thời gian với giá trị được gửi. TOTP có giá trị trong một khoảng thời gian ngắn.

- With Counter-Based One Time Passwords (HOTP), Bản dựng Keycloak của Red Hat sử dụng bộ đếm dùng chung thay vì thời gian hiện tại. Bản dựng Red Hat của máy chủ Keycloak tăng bộ đếm mỗi lần đăng nhập OTP thành công. OTP hợp lệ sẽ thay đổi sau khi đăng nhập thành công.

- TOTP an toàn hơn HOTP vì OTP phù hợp có hiệu lực trong một khoảng thời gian ngắn, trong khi OTP cho HOTP có giá trị trong một khoảng thời gian không xác định. HOTP thân thiện với người dùng hơn TOTP vì không có giới hạn thời gian để nhập OTP.

- HOTP yêu cầu cập nhật cơ sở dữ liệu mỗi khi máy chủ tăng bộ đếm. Bản cập nhật này làm giảm hiệu suất trên máy chủ xác thực khi tải nặng. Để tăng hiệu quả, TOTP không ghi nhớ mật khẩu đã sử dụng nên không cần thực hiện cập nhật cơ sở dữ liệu. Hạn chế là có thể sử dụng lại TOTP trong khoảng thời gian hợp lệ

