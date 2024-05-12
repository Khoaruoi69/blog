---
layout: post
title: Các câu lệnh Linux khác
categories: Series-DevOps
---

####  Các câu lệnh thông dụng


**Kiểm tra trạng thái xử dụng RAM trên Server**

- Lệnh: free -m 

![_config.yml]({{ site.baseurl }}/images/linux-06-1.png)

**Kiểm tra Server còn lại bao nhiêu GB**

- Lệnh: df -h /

![_config.yml]({{ site.baseurl }}/images/linux-06-2.png)

**Lệnh giống như task manager**

- Lệnh: top

- Thoát khỏi tiến trình: Ctrl + C

![_config.yml]({{ site.baseurl }}/images/linux-06-3.png)

**Đổi tên của Server**

- Lệnh: sudo hostnamectl set-hostname lab-server (note: phải khởi động lại server để apply)

**Khỏi động lại Server**

- Lệnh: sudo reboot

![_config.yml]({{ site.baseurl }}/images/linux-06-4.png)

**Qua tài khoản Admin**

- Lệnh: sudo -i (note: nhập password rr tiếp tục)

**netstat -tlpun**

- t: Hiển thị các thông tin kết nối tcp,

- l: hiển thị các công đang mở và lắng nghe chấp nhận kết nối,
 
 - p: hiển thị tiến trình và chương trình liên quan đến mỗi kết nối, 

 - u: là hiển thị thông tin các kết nối của PID, 

 - n: là hiển thị địa chỉ IP và cổng trong dạng số thay vì tên miền, tên cổng

![_config.yml]({{ site.baseurl }}/images/linux-06-5.png)

**Các tiến trình chạy trên hệ thống**

- Lệnh: ps - ef

![_config.yml]({{ site.baseurl }}/images/linux-06-6.png)


**Kiểm tra kết nối internet**

- Lệnh: ping 8.8.8.8

![_config.yml]({{ site.baseurl }}/images/linux-06-7.png)

**Check các kết nối** khi triển khai dự án. Server có thông kết nối ko 

- Lệnh: telnet 192.x.x.x

![_config.yml]({{ site.baseurl }}/images/linux-06-8.png)

**Dùng trong TH: Telnet ko thành công, nhưng Server có tồn tại**

- Lệnh: traceroute - T - P  ( -T: chính là TCP, -P : port)

![_config.yml]({{ site.baseurl }}/images/linux-06-9.png)


**Cài đặt các công cụ**

- Lệnh: apt install *tên công cụ*

**Xóa công cụ**

- Lệnh: apt remove *tên công cụ*

![_config.yml]({{ site.baseurl }}/images/linux-06-10.png)