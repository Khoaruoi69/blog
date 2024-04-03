---
layout: post
title: Các lệnh Linux thông dụng
categories: Series-DevOps
---

### Các lênh cơ bản

- Đầu tiên kết nối ssh vào server

- **pwd**:cho biết vị trí hiện tại ở đâu

- **whoami**: cho biết đang đăng nhập vs user nào

- **Hệ thống file**

![_config.yml]({{ site.baseurl }}/images/linux-sys-file.png)

- **cd**: di chuyển qua lại các thư mục ( VD: cd /, cd /var, cd .., cd )

![_config.yml]({{ site.baseurl }}/images/linux-pwd.png)

- **ls**: xem nội dung thư mục (VD: ls , ls /var) *chú ý chỉ cần tab là tự gợi ý, nhấn nút lên xuống để gọi lại các lệnh đã gõ*

+ **ls -l**: hiện nội dung theo danh sách. VD: ls -l /var

+ **ls -a**: hiển thị các file thư mục ẩn. VD: ls - a /

+ **ls -t**: sắp xếp từ mới nhất đến cũ nhất . VD: ls -l /

+ **ls -lta**: này là kết hợp, hiển thị file theo danh sách sắp xếp từ mới nhất đến cũ nhất và hiển thị các thư mục ẩn

![_config.yml]({{ site.baseurl }}/images/linux-ls.png)

- **mkdir**: tạo một thư mục. *VD: mkdir data*

+ **mkdir -p**: tạo nhiều thư mục cùng 1 lúc. *VD: mkdir -p data/data1/data2*

![_config.yml]({{ site.baseurl }}/images/linux-mkdir.png)


- **touch**: tạo file. *VD: touch data/data.txt*

- **rm**: xóa file. *VD: rm data/data.txt*

+ **rm -r**: xóa thư mục. *VD: rm -r data/data1/data2*

+ **rm -rf**: xóa thư mục mà hệ thống ko cần hỏi. 

![_config.yml]({{ site.baseurl }}/images/linux-touch-rm.png)

- **cp**: dùng để copy file

+ **cp -r**: copy thư mục

![_config.yml]({{ site.baseurl }}/images/linux-cp.png)

- **mv**: di chuyển file và đổi tên. 
VD: di chuyển file data.txt ở /tmp/data.txt sang home và đổi tên thành data1.txt:
 mv /tmp/data.txt data1.txt

Di chuyển folder data và đổi thành data1: mv /tmp/data/ data1

![_config.yml]({{ site.baseurl }}/images/linux-mv.png)

- **echo**: in nội dung ra màng hình hoặc ghi nội dung vào file (echo "content you want to write" > fileName.txt,
nếu muốn ghi xuống dòng tiếp theo trong file cũ thì: echo "content you want to write line 2" >> fileName.txt)

- **cat**: xem nội dung file 

![_config.yml]({{ site.baseurl }}/images/linux-echo.png)

- **history**: xem những câu lệnh từng gõ

- **tail**: xem đc nội dung trong file

+ **tail -n**: xem số dòng muốn xem. *VD" tail -n 1 title.txt*. Lấy ra 1 dòng gh vào file log,txt: tail -n 1 title.txt > log,txt

+ **tail -f**: xem dữ liệu real time. ( thoát khỏi: Ctrl + C)

![_config.yml]({{ site.baseurl }}/images/linux-tail.png)

- **sudo**: sudo + 1 lệnh nào đó sẽ sử dụng root để thực hiện (sử dụng các cậu lệnh với quyền Admin)

![_config.yml]({{ site.baseurl }}/images/linux-sudo.png)