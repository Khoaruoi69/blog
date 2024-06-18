---
layout: post
title: Docker part 2 - Dockerfile
categories: docker
---

### Dockerfile

### 2.1 Docker file là gì ?

Thì đơn giản, Dockerfile là một **fil**e, dạng text, không có đuôi, giúp thiết lập **cấu trúc** cho **docker image** nhờ chứa một tập hợp các **câu lệnh**.

Từ những câu lệnh đó, Docker có thể thực hiện **đóng gói** một docker images theo yêu cầu tùy biến của riêng bạn.


![_config.yml]({{ site.baseurl }}/images/docker-df-01.png)

=> Như vậy Dockerfile sẽ **quy định** Docker image được khởi tạo từ đâu, gồm những gì trong đó.

### 2.2 Cách viết Dockerfile

Demo như sau:

![_config.yml]({{ site.baseurl }}/images/docker-df-02.png)

+ Đầu tiên chúng ta sẽ viết Dockerfile để tạo nên image rồi tạo nên container, sau khi tạo được container rồi thì đồng nghĩa là đã tạo ra được máy ảo để bạn có thể khởi chạy ứng dụng của bạn trên máy ảo đó

+ Thư mục webroot chứa mã nguồn chương trình, có thể là một c++ app, java app hoặc web app được viết bằng php hoặc ruby,.... (Ở đây, để cho đơn giản, chúng ta chỉ đặt file hello.html, chạy trên trình duyệt sẽ hiển thị dòng Hello Word)

Sau này, bạn dùng editor để lập trình trên máy thật, chỉnh sửa mã nguồn trong thư mục này, mọi sự thay đổi được cập nhật ngay lập tức trên máy ảo.

+ File start.sh chứa những câu lệnh được chạy khi bật container (có thể dùng để start mysql, nginx, redis ...)

Nào, bắt đầu viết Dockerfile nhé:

### 2.2.1 Thiết lập image gốc

Đầu tiên, ta cần khai báo *thằng cha* của image này là thằng nào, tức là nó **khởi nguồn từ đâu**, sử dụng:

**FROM**

**Image gốc** có thể là centos: 7, ubuntu:16.04, vân vân mây mây.

ví dụ:

- FROM ubuntu:16.04

Có thể bạn sắp biết, **Docker hub** - nơi lưu trữ và chia sẻ các image sẽ chứa những **image gốc** mà từ đó, bạn có thể *phát triển, cài cắm, thay tháo, chỉnh sửa, thêm bớt* để tạo ra những images tùy biến cho riêng bạn.

Khi Docker đọc tới câu lệnh này, nó sẽ tự động tìm xem image ubuntu:16.04 này đã tồn tại trong máy chưa, nếu chưa thì Docker sẽ tự động pull image này về. Trong đó ubuntu là tên của image, 16:04 là tag, bạn cũng có thể hiểu nó nôm na như là branch trong git.

![_config.yml]({{ site.baseurl }}/images/docker-df-03.png)

 **MAINTAINER :** Một optional dùng để đặt tên cho tác giả của Dockerfile mà bạn đang viết. Ví dụ:

 - MAINTAINER HoanKi<hoanki2212@gmail.com>

 ### 2.2.2 Cài đặt ứng dụng

 Bây giờ, chúng ta sẽ **cài thêm** các ứng dụng, **thiết lập** môi trường cần thiết trên ubuntu:16.04 này

Bạn có thể cài nginx, php, python, ruby, java ... phụ thuộc vào nhu cầu của bạn, sử dụng:

+ **RUN :** Để thực thi một câu lệnh nào đó trong quá trình build images.

+ **CMD :** Để thực thi một câu lệnh trong quá trình **bật container**.

Mỗi Dockerfile chỉ có một câu lệnh CMD, nếu như có nhiều hơn một câu lệnh CMD thì chỉ có câu lệnh CMD cuối cùng được sử dụng.

Một câu hỏi đặt ra là nếu tôi muốn khởi động nhiều ứng dụng khi start container thì sao, lúc đó hay nghĩ tới ENTRYPOINT

+ **ENTRYPOINT:** Để thực thi một số câu lệnh trong quá trình start container, những câu lệnh này sẽ được viết trong file .sh.

ví dụ:

```java
# Update ubuntu
RUN apt-get update

# Install nginx
RUN apt-get install -y nginx

# Install mysql server
RUN echo "mysql-server mysql-server/root_password password root" | debconf-set-selections \
    && echo "mysql-server mysql-server/root_password_again password root" | debconf-set-selections \
    && apt-get install -y mysql-server

```

Trong khi cài nginx, sẽ có câu hỏi xuất hiện và bạn cần trả lời yes/no, khi đó tùy chọn -y trong **RUN apt-get install -y nginx** sẽ thể hiện cho sự lựa chọn **yes** của bạn.

### 2.2.3 Cấu hình

+ **EXPOSE:** Container sẽ lắng nghe trên các cổng mạng được chỉ định khi chạy
+ **ADD :** Copy file, thư mục, remote file thêm chúng vào filesystem của image.
+ **COPY :** Copy file, thư mục từ host machine vào image. Có thể sử dụng url cho tập tin cần copy.
+ **WORKDIR :** Định nghĩa directory cho **CMD**
+ **VOLUME** Mount thư mục từ máy host vào container

Tạo file.sh

Như ở phần entrypoint đã nói, cho dù chỉ cần một câu lệnh mình vẫn dùng ENTRYPOINT, để sau này dễ dàng tùy biến, phát triển.

Tạo file.sh như sau :

```java
#!/bin/bash
service nginx start
exec $@

```

Ta có ví dụ ở phần này như sau:

```java
ADD start.sh /venv

WORKDIR /venv

RUN chmod a+x /venv/*

ENTRYPOINT ["/venv/start.sh"]

EXPOSE 80

```

Tổng hợp lại, ta có một ví dụ cho Dockerfile như sau :

```java
FROM ubuntu:16.04

MAINTAINER HoanKi<hoanki2212@gmail.com>

RUN DEBIAN_FRONTEND=noninteractive

RUN apt-get update

RUN apt-get install -y nginx

RUN echo "mysql-server mysql-server/root_password password root" | debconf-set-selections \
    && echo "mysql-server mysql-server/root_password_again password root" | debconf-set-selections \
    && apt-get install -y mysql-server

WORKDIR /venv

COPY start.sh /venv

RUN chmod a+x /venv/*

ENTRYPOINT ["/venv/start.sh"]

EXPOSE 80

```

+ Tạo file hello.html trong thư mục webroot:

- *<h1>Hello word</h1>*

+ Mình có push code mẫu lên GitHub:  https://github.com/Khoaruoi69/docker_tutorial
+ Còn đây là full document về: https://docs.docker.com/reference/dockerfile/

### 2.3 Cách sử dụng Dockerfile

### 2.3.1 Build docker image từ Dockerfile

Ta sử dụng câu lệnh sau:

- sudo docker build -t <image_name> .

Ví dụ: *sudo docker build -t ubuntu-nginx .*

Bạn có thể dùng lệnh: **docker images** để xem kết quả

![_config.yml]({{ site.baseurl }}/images/docker-df-04.png)

### 2.3.2 Tạo container từ image.

Gõ lệnh theo syntax:

- sudo docker run -v <forder_in_computer>:<forder_in_container> -p <port_in_computer>:<port_in_container> -it <image_name> /bin/bash

Trong đó:

+ -v : Thể hiện việc mount volume, dữ liệu từ thư mục từ máy thật có thể được truy cập từ thư mục của máy ảo.
+ -p: Cổng mạng từ máy thật để dẫn tới cổng mạng của máy ảo đang chạy.
+ -t: Chạy container và mở terminal bằng /bin/bash
+ Ví dụ vào localhost mặc định của nginx:

**sudo docker run -p 9000:80 -it ubuntu-nginx /bin/bash**

Kiểm tra log trên Terminal:

![_config.yml]({{ site.baseurl }}/images/docker-df-05.png)

Trên trình duyệt:

![_config.yml]({{ site.baseurl }}/images/docker-df-06.png)

- Ví dụ vào thư mục dự án ở máy thật:

**sudo docker run -v  /media/hoanki/PROJECT4/GitRepo/docker_tutorial/webroot:/var/www/html -p 9000:80 -it ubuntu-nginx /bin/bash**

Thay thế **/media/hoanki/PROJECT4/GitRepo/docker_tutorial/webroot** cho đúng với trên máy bạn nhé !

![_config.yml]({{ site.baseurl }}/images/docker-df-07.png)


### 3. Docker Hub

Docker-hub: Nơi lưu trữ và chia sẻ các image của Docker, nhưng không chỉ có vậy.

Nãy giờ bạn và tôi đang build image hoàn toàn dưới local, nhưng Docker Hub còn hỗ trợ chúng ta làm việc này trên server nữa.

+ Mình tạo mới repo docker-basic trên Github, như này:

![_config.yml]({{ site.baseurl }}/images/docker-df-08.png)

+ Sau đó mình tạo mới một repo trên Dockerhub.(https://hub.docker.com/)

Vào Create chọn Create Automated Build, chọn Github rồi trỏ tới docker-basic bạn vừa tạo ở GitHub.

![_config.yml]({{ site.baseurl }}/images/docker-df-09.png)

- Và ta có docker_basic, trông như sau:

![_config.yml]({{ site.baseurl }}/images/docker-df-10.png)

+ Dockerhub sẽ hỗ trợ bạn build docker image online, sau đó bạn có thể pull nó về để sử dụng.
+ Vào tab Build Settings,

![_config.yml]({{ site.baseurl }}/images/docker-df-11.png)

+ Từ giờ trở đi, mỗi khi bạn push code lên branch nào trên github branch đó bạn đã setting trên DockerHub thì images sẽ tự động được build.

Việc build này sẽ tự động thực hiên trên server Docker Hub nhé, ví dụ mình push code lên branch init_dockerfile thì Dockerhub tự động build image, kết quả có ở trong tab Build Detail.

Mình tạo pull request, sau khi mình merge nó thì DockerHub cũng sẽ build image tiếp trên branch master.

+ Thử đi nhé, đôi khi buid ở local thì pass mà build trên server thì lỗi, cảm giác fix mãi nó mới hiện dòng chữ "Success" nó mới awesome làm sao

![_config.yml]({{ site.baseurl }}/images/docker-df-12.png)
