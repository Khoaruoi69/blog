---
layout: post
title: Array Data Structures
categories: Data_Structures
---

### Array - Cấu trúc dữ liệu dạng mảng

- Một mảng (**array**) là một tập hợp các phần tử được lưu trữ liên tiếp nhau trong một bộ nhớ. 
Ý tưởng của mảng là lưu các phần tử cùng loại. Mỗi phần tử được xác định bởi chỉ mục của chúng trong mảng.

![_config.yml]({{ site.baseurl }}/images/array-data-structure.png)

#### 1. Tạo chỉ mục cho mảng 

- 0 (zero-based indexing): Phần tử đầu tiên trong mảng có chỉ số là 0. 

- 1 (one-based indexing): Phần tử đầu tiên trong mảng có chỉ số là 1.

- n (n-based indexing): Chỉ số của mảng được tự do lựa chọn.

### 2. Ưu điểm sử dụng mảng 

- Mảng cho phép truy cập ngẫu nhiên các phần tử. Điều này giúp truy cập các phần tử nhanh hơn khi biết vị trí của chúng.

- Mảng có cache locality tốt giúp tạo ra sự khác biệt khá lớn về performance.

- **Lưu ý:** Thông thường, một mảng các ký tự thường được gọi là **chuỗi**, còn một mảng số nguyên hay số thực thì chỉ đơn giản gọi là **mảng.**

### Array - Mảng trong Java

Trong Java, một mảng (array) là một tập hợp các phần tử có cùng kiểu dữ liệu. **Nếu kiểu dữ liệu là kiểu nguyên thủy thì các phần tử có địa chỉ liên tiếp nhau trên bộ nhớ (memory). Nếu kiểu dữ liệu là dạng đối tượng của lớp, thì các đối tượng thực tế được lưu trữ trên bộ nhớ heap.** Mảng có **số phần tử cố định và bạn không thể thay đổi kích thước của nó**.

**Note:**

- **Bộ nhớ head là gì ??**

*Trong Java, "heap" là một vùng bộ nhớ được sử dụng để lưu trữ đối tượng và các dữ liệu được cấp phát động. Đây là nơi mà các đối tượng được tạo ra trong chương trình Java được lưu trữ. Bộ nhớ heap là một phần của bộ nhớ runtime của Java và tồn tại từ khi chương trình bắt đầu chạy cho đến khi kết thúc.Trong heap, các đối tượng được cấp phát bằng cách sử dụng từ khóa "new" và chúng sẽ tồn tại cho đến khi không còn tham chiếu nào đến chúng. Java tự động quản lý bộ nhớ heap thông qua việc dọn dẹp bộ nhớ không sử dụng thông qua một tiến trình gọi là "garbage collection" (thu gom rác). Garbage collection giúp giải phóng bộ nhớ của các đối tượng không sử dụng, giúp cho ứng dụng Java tránh được vấn đề memory leaks.*

#### 1. Đặc điểm của mảng trong Java

- Các phần tử của mảng được đánh chỉ số (index), bắt đầu từ chỉ số 0. Bạn có thể truy cập vào các phần tử của nó thông qua chỉ số.

- Một mảng có thể được khai báo như một trường tĩnh (static field), một biến cục bộ (local variable), hoặc làm tham số cho phương thức.

- Kích thước của mảng phải được chỉ định bởi một giá trị int, không phải long hoặc short.

- Một mảng có thể chứa dữ liệu kiểu nguyên thủy (primitives data types), hoặc các đối tượng của các lớp (object), tùy thuộc vào khai báo mảng. Trong trường hợp dữ liệu kiểu nguyên thủy, các giá trị thực được lưu trữ liên tiếp nhau trên bộ nhớ. Trong trường hợp dữ liệu dạng đối tượng, thì các đối tượng thực tế được lưu trữ trên bộ nhớ heap.

### ArrayList – Lớp ArrayList trong Java

Lớp **ArrayList** trong Java được xây dựng dựa trên cấu trúc dữ liệu dạng **Array (mảng)**. Lớp **ArrayList** được sử dụng rộng rãi vì các chức năng và tính linh hoạt của nó. Hầu hết các lập trình viên chọn **ArrayList** hơn là chọn **Array** vì nó là một sự thay thế rất tốt cho các mảng truyền thống trong Java. Lớp **ArrayList** sử dụng một mảng động, **có thể thay đổi kích thước**, để lưu trữ các phần tử. Nó là một phần của **Java Collections Framework.**

![_config.yml]({{ site.baseurl }}/images/array-data-structure-2.png)

#### 1. Các đặc điểm của ArrayList

- Lớp **ArrayList** là một mảng có thể **tái kích thước**, còn được gọi là **một mảng động.** Nó tăng kích thước của nó để chứa các phần tử mới và thu nhỏ kích thước khi các phần tử được loại bỏ.

- Lớp **ArrayList** cho phép các giá trị trùng lặp và null.

- Lớp **ArrayList** duy trì thứ tự **insertion** các phần tử. Tức là, phần tử đầu tiên sẽ được thêm vào vị trí đầu tiên.
Không thể tạo một **ArrayList** lưu trữ dữ liệu kiểu nguyên thủy như int, char,… mà phải sử dụng kiểu **wrapper** như Integer, Boolean, Character,…

- Lớp **ArrayList** không **thread-safe.** Tức là, bạn không thể share chúng giữa nhiều luồng (thread) mà không được đồng bộ hóa. Để đồng bộ hóa lớp **ArrayList** bạn có thể sử dụng phương thức **Collections.synchronizedList().**

- Lớp **ArrayList**, giống như Array, cho phép truy cập ngẫu nhiên vào danh sách, lấy các phần tử theo chỉ mục (index) của chúng.