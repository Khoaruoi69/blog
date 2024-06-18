---
layout: post
title: Docker part 1 - History
categories: docker
---

### Tại sao nên sử dụng docker ??

Khi dự án bạn đang làm cái đang cài cắm rất nhiều thứ như php, npm, redis, mysql, elasticsearch ...

Mỗi thứ bạn phải biết được version bao nhieeum, một tá thư viện, vân vân và mây mây  với hàng tá thứ khác.
Điều này làm bạn mất kha khá thời gian để setup chúng. 

Bây giờ giải pháp cho bạn đó chính là Docker, nó sẽ làm mọi thứ đơn giản hơn nhiều.

### 2. Quá trình hình thành

Học lại lịch sử một chút đã nhé:

#### 2.1 Containerlization là gì ?

- 1. Ngày xưa, mô hình máy chủ thường là: 

**Máy chủ vật lý (physical server) + hệ điều hành (Operating system) + ứng dụng (application)**

![_config.yml]({{ site.baseurl }}/images/docker-his-01.png)

- Vấn đề gặp phải ở đây là lãng phí tài nguyên

+ Một máy chủ chỉ cài được một OS
+ Cho dù có ổ cứng khủng, ram khủng thì cũng không tận dụng hết lợi thế.

- 2. Sau đó ra đời công nghệ ảo hóa **virtualization.**

+ Có thể bạn đã nghe tới cái tên **Virtualbox** hay **VMware** rồi đúng không, đó đó chính nó đó
+ Với công nghệ này, trên một **máy chủ vật lý** mình có thể cài đặt được nhiều OS, tận dụng tài nguyên đã tốt hơn.

![_config.yml]({{ site.baseurl }}/images/docker-his-02.png)

- Ví dụ:

+ Bạn đang cài đặt **Ubuntu** trên máy tính để lập trình, sau đó cài **VirtualBox** để có thể
   - Cài thêm Windows 10 - thi thoảng nhớ mùi còn vào làm trận game
   - Cài thêm CenOS - thử trải nghiệm nhánh RedHat của họ nhà Linux xem sao do Ubuntu thuộc nhánh Debian.
   - Đó, vầy là trên một máy tính, bạn đã cài nhiều hệ điều hành để phục vụ cho những mục đích khác nhau.

- Nhưng lại nảy sing vấn đề tiếp:

+ **Về tài nguyên**
    - Khi bạn bật máy ảo trên **VitualBox** chẳng hạn, bạn cần cấu hình để cung cấp ngay từ đầu tài nguyên **ổ cứng** và **ram** từ máy thật cho máy ảo.
    - Bật máy ảo lên rồi để đó không làm gì thì máy thật cũng không thể tái sử dụng tài nguyên đã cho đi
    - Ví dụ khi tạo một**máy ảo ram 2GB** trên **máy thật ram 6GB**, lúc này máy thật sẽ chỉ còn **tối đa 6 - 2 = 4 GB ram**, kể cả khi máy ảo không dùng hết **2GB ram** được cấp, đó là một sự lãng phí.

+ **Về thời gian**
    -  Việc khởi động, tắt máy ảo khá lâu, có thể lên tới hàng phút.

- 3. Ở bước **tiến hóa** tiếp theo, người ta sinh ra công nghệ **containerlization**

+ Với công nghệ này, trên một máy chủ vật lý, ta sẽ cài đặt được nhiều máy ảo (giống với công nghệ ảo hóa **virtualization**), nhưng tốt hơn ở chỗ là các máy con này (Guess OS) đều **dùng chung** phần nhân của máy mẹ (Host OS) và chia sẻ với nhau tài nguyên máy mẹ.

![_config.yml]({{ site.baseurl }}/images/docker-his-03.png)

+ Có thể nói là khi nào cần tài nguyên thì được cấp, cần bao nhiêu thì cấp bấy nhiêu, như vậy việc tận dụng tài nguyên đã tối ưu hơn. Điểm nổi bật nhất của **containerlization** là nó sử dụng các **container**, và một kĩ sư của Google đã phát biểu rằng:

![_config.yml]({{ site.baseurl }}/images/docker-his-04.png)

Một công ty hàng đầu về công nghệ đã áp dụng nó, chứng tỏ lợi ích, độ tin cậy của công nghệ này rồi nhé ! Chúng ta cùng áp dụng nó thôi.

### 2.2 Container là gì ??

Các phần mềm, chương trình sẽ được **Container Engine** ( là một công cụ ảo hóa tinh gọn được cài đặt trên host OS) đóng gói thành các **container**.

Thế **Container** là gì, nó là một giải pháp để chuyển giao phần mềm một cách đáng tin cậy giữa các **môi trường** máy tính khác nhau bằng cách:

+ Tạo ra một môi trường chứa **mọi thứ** mà phần mềm cần để có thể chạy được.
+ Không bị các yếu tố liên quan đến môi trường hệ thống làm ảnh hưởng tới.
+ Cũng như không làm ảnh hưởng tới các phần còn lại của hệ thống.

Bạn có thể hiểu là ruby, rails, mysql ... kia được bỏ gọn vào một hoặc nhiều cái thùng (container), ứng dụng của bạn chạy trong những chiếc thùng đó, đã có sẵn mọi thứ cần thiết để hoạt động, không bị ảnh hưởng từ bên ngoài và cũng không gây ảnh hưởng ra ngoài.

Các tiến trình (process) trong một **container** bị cô lập với các tiến trình của các container khác trong cùng hệ thống tuy nhiên tất cả các container này đều chia sẻ kernel của host OS (dùng chung host OS)

Đây một nền tảng mở dành cho các lập trình viên, quản trị hệ thống dùng để xây dựng, chuyển giao và chạy các ứng dụng **dễ dàng** hơn. Ví dụ, bạn có một app java, bạn sẽ không cần cài đặt JDK vào máy thật để chạy app đó, chỉ cần **kiếm** container đã được setting tương ứng cho app về, **bật nó lên**, cho app **chạy** bên trong môi trường **container** đó, vậy là ok. Khi không sài nữa thì **tắt hoặc xóa** bỏ container đó đi, không ảnh hưởng gì tới máy thật của bạn.

**Ưu điểm**

+ **Linh động:** Triển khai ở bất kỳ nơi đâu do sự phụ thuộc của ứng dụng vào tầng OS cũng như cơ sở hạ tầng được loại bỏ.
+ **Nhanh:** Do chia sẻ host OS nên container có thể được tạo gần như một cách tức thì. Điều này khác với vagrant - tạo môi trường ảo ở level phần cứng, nên khi khởi động mất nhiều thời gian hơn
+ **Nhẹ:** Container cũng sử dụng chung các images nên cũng không tốn nhiều disks.
+ **Đồng nhất:** Khi nhiều người cùng phát triển trong cùng một dự án sẽ không bị sự sai khác về mặt môi trường.
+ **Đóng gói:** Có thể ẩn môi trường bao gồm cả app vào trong một gói được gọi là container. Có thể test được các container. Việc bỏ hay tạo lại container rất dễ dàng.

**Nhược điểm**

Xét về tính an toàn:

+ Do dùng chung OS nên nếu có lỗ hổng nào đấy ở kernel của host OS thì nó sẽ ảnh hưởng tới toàn bộ container có trong host OS đấy.
+ Ngoài ra hãy thử tưởng tượng với host OS là Linux, nếu trong trường hợp ai đấy hoặc một ứng dụng nào đấy có trong container chiếm được quyền superuser, điều gì sẽ xảy ra? Về lý thuyết thì tầng OS sẽ bị crack và ảnh hưởng trực tiếp tới máy host bị hack cũng như các container khác trong máy đó (hacker sử dụng quyền chiếm được để lấy dữ liệu từ máy host cũng như từ các container khác trong cùng máy host bị hack chẳng hạn).

### 2.3 Docker ra đời

Công nghệ ảo hóa (virtualization) thì ta có thể dùng công cụ Virtualbox hay VMware thế còn đối với containerlization thì dùng gì đây ? Google họ dùng gì ?

Oh mình không biết được, mỗi một ông lớn có một cách để áp dụng công nghệ này và họ private source code.

Gần đây, mà cũng lâu rồi 😄 Có một công ty tiến hành public source code của họ về công nghệ này, họ tung ra sản phẩm mang tên là Docker và nhận được nhiều sự chú ý.

![_config.yml]({{ site.baseurl }}/images/docker-his-05.png)

Sau đó công ty cũng đổi tên thành Docker luôn. Công ty này cung cấp công cụ Docker free nhưng họ kiếm được rất nhiều tiền từ những dịch vụ khác đi kèm với nó.

Với sự bùng nổ của việc sử dụng container cùng với những lợi ích lớn mà nó mang lại, gã khổng lồ phần mềm Microsoft không muốn bỏ qua cơ hội màu mỡ này với việc cho ra mắt tính năng mới có tên Windows Container.

### 3. Cài đặt ra sao ??

+ **Docker** hỗ trợ nhiều nền tảng hệ điều hành khác nhau bao gồm **Linux, Windows và cả Mac**. Ngoài ra, Docker còn hỗ trợ nhiều dịch vụ điện toán đám mây nổi tiếng như Microsoft Azure hay Amazon Web Services.

+ Lưu ý là ban đầu nó được xây dựng trên nền tảng Linux. Vì Docker cần can thiệp vào phần lõi, nhân Kernel trong khí đó Linux là mã nguồn mở, gọi là cần gì có nấy.

+ Đến khi thấy Docker hay quá, bác Windows ngỏ lời, thế là công ty Docker và công ty Microsoft hợp tác với nhau nhưng nghe chừng chưa khả quan lắm bởi vì nhân Windows có nhưng thứ không public được ( bản quyền mà ).

+ Cho tới hiện tại khi cài Docker trên Windows hay Mac thì Docker sẽ cài một máy ảo Linux trên máy thật và Docker hoạt động dựa trên máy ảo Linux đó. Còn tương lai về sau thì ai biết được.

+ Docker có 2 phiên bản, CE( dành cho nhà phát triển, nhóm nhỏ, coder như chúng ta) và EE (dành cho doanh nghiệp).

Dưới đây mình sẽ giới thiệu cài đặt đối với bản CE trên Ubuntu thông qua repository như sau:

```java
I. Chuẩn bị một chút:

# Update the apt package index:
$ sudo apt-get update

# Install packages to allow apt to use a repository over HTTPS:
$ sudo apt-get install \
   apt-transport-https \
   ca-certificates \
   curl \
   software-properties-common

# Add Docker’s official GPG key:
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Verify that you now have the key with the fingerprint 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88, by searching for the last 8 characters of the fingerprint.
$ sudo apt-key fingerprint 0EBFCD88

pub   4096R/0EBFCD88 2017-02-22
     Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22

# Use the following command to set up the stable repository
$ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"

```

```java
II. Cài đặt docker CE:

## Update the apt package index:
$ sudo apt-get update

## Install the latest version of Docker CE, or go to the next step to install a specific version. Any existing installation of Docker is replaced.
$ sudo apt-get install docker-ce

## Verify that Docker CE is installed correctly by running the hello-world image.
$ sudo docker run hello-world

```

Nếu câu lệnh cuối cùng của bạn ra kết quả như sau thì bạn đã cài đặt Docker thành công:

![_config.yml]({{ site.baseurl }}/images/docker-his-06.png)

### 4. Hoạt động như thế nào ?

+ **Docker image** là nền tảng của **container**, có thể hiểu Docker image như **khung xương** giúp định hình cho container, nó sẽ tạo ra container khi thực hiện câu lệnh chạy image đó. Nếu nói với phong cách lập trình hướng đối tượng, Docker image là **class**, còn container là **thực thể** (**instance, thể hiện**) của class đó.

Docker có hai khái niệm chính cần hiểu, đó là **image** và **container**:

+ **Container:** Tương tự như một *máy ảo*, xuất hiện khi mình **khởi chạy image**.
**Tốc độ** khởi chạy container nhanh hơn tốc độ khởi chạy **máy ảo** rất nhiều và bạn có thể thoải mái chạy 4,5 container mà không sợ treo máy.
Các **files** và **settings** được sử dụng trong container được lưu, sử dụng lại, gọi chung là images của docker.

+ Image: Tương tự như file .gho để ghost win mà mấy ông cài win dạo hay dùng.
Image này không phải là một file vật lý mà nó chỉ được chứa trong Docker.
Một image bao gồm hệ điều hành (Windows, CentOS, Ubuntu, …) và các môi trường lập trình được cài sẵn (httpd, mysqld, nginx, python, git, …).

