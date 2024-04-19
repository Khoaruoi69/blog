---
layout: post
title: Doc comment và Javadoc trong Java
categories: Java-8
---

### Documentation comment

Documentation coment gọi tắt là Doc Comment, chúng ta hiển nó là cách comment code theo kiểu document. Đây là 1 cách comment đặc biệt, khác với kiểu **//Text** là
comment trên 1 dòng hay **/*Text/** commnet trên nhiều dòng code. Kiểu **Doc Comment** được ghi theo format **/** Document*/**.

Tất cả các kiểu comment đều có 1 đặc điểm giống nhau là khi build, trình biên dịch sẽ bỏ qua chúng, không build comment vào filed build cuối cùng.
Nhưng khác với anh em trong họ comment, **Doc Comment** không đơn thuần chỉ là đểm comment, chúng được dùng trong một chuyện khác. Công dụng cụ thể
của **Doc Commnent** sau đây:

VD: Đây là ví dụ comment theo kiểu Doc comment

```java
/**
 * The MainClass is the class that help to print out the "Hello World!" text
 * 
 * @author yellowcode
 * @version 1.0
 * @since 2021-05-04
 *
 */
public class MainClass {

    /**
     * The main function, entry point of this app
     * @param args
     */
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```

#### Công dụng của Doc Comment


**Các lợi ích của Doc Comnent**

+ **Thứ nhất** về mặt giải thích code bạn đang làm gì, thì **Doc Comment** sẽ luôn rõ ràng hơn do chúng có được sự hỗ trợ về mặt định dạng nổi bật hơn cho các tham số.

![_config.yml]({{ site.baseurl }}/images/javadoc-1.png)

+ **Thứ hai** là lợi ích về mặt sử dụng các dòng code có comment theo kiểu **Doc Comment** này, thì khi sử dụng các thành phần được comment *Chuẩn*, bạn sẽ thấy comment,
hay document sẽ xuất hiện ở ngữ cảnh của InteiJ IDE ( bạn dễ dàng nhìn thấy các docummnent này khi đưa chuột vào lớp hay hàm có **Doc Commnent**).

![_config.yml]({{ site.baseurl }}/images/javadoc-2.png)

![_config.yml]({{ site.baseurl }}/images/javadoc-3.png)

+ **Thứ ba** về mặt xuất xưởng các thư viện. **Doc Commnent** sẽ được một công cụ có tên **javadoc** build ra một trang mô tả theo kiểu HTML. Nó là một trang Web được xây dựng theo kiểu HTML. Nó là một trang Web được xây dựng hoàn chỉnh và bạn có thể dùng để publish hay nhúng và trang Web khác. Rất thích hợp để tạo ra các thư viện Java và gửi đến người dùng thư viện của bạn với đầy đủ các hướng dẫn sử dụng các Java code mà bạn xây dựng. 

#### Tạo HTML Docummnet trên InteliJ

Khi có 1 số Doc Comment trong soure code. Chọn menu **Tool > Generate javaDoc ...**

![_config.yml]({{ site.baseurl }}/images/javadoc-4.png)

Một cửa sổ sẽ xuất hiện, bạn hãy để nguyên như mặt định. Chúng ta thiết lập phạm vi áp dụng để tạo HTML document (scope), cấp độ chia sẻ private/package/protected/public.
Và thiết lập nơi mà thành phẩm HTML document được trích xuất ra, bạn có thể chỉ định vào thư mục **/doc** bên trong project của bạn như dưới đây.

![_config.yml]({{ site.baseurl }}/images/javadoc-5.png)

Sau khi nhấn **OK** ở trên của sổ, bạn sẽ thấy ngay lập tức Web Browser mặc định trên máy bạn được mở ra với nội dung chính là giới thiệu về project của bạn với các **Doc Commnent** trong đó.

![_config.yml]({{ site.baseurl }}/images/javadoc-6.png)

OK vậy tiếp theo hãy thử các mục bên trong trang Web để xem Javadoc giúp tạo các hướng dẫn cho code của chúng ta như thế nào.


#### Định dạng Java Doc thông qua sử dụng Tag

**Tag** giống như các tham số giúp cho **Javadoc** có thể tạo các HTML và truyền các định nghĩa của từng **Tag** vào HTML. Các **Tag** trong *Javadoc* thường ko có ràng buộc gì cả, chỉ cần thêm nhưng nơi bạn cần HTML làm nổi bậc chúng lên.



#### @author, @version, @since

+ **@author:** dùng để hiển thị thông tin tác giả.

+ **@version:** dùng để hiển thị version của document.

+ **@since:** dùng hiển thị ngày hoặc version tạo ra document.

![_config.yml]({{ site.baseurl }}/images/javadoc-7.png)

#### {@code}, @param

+ **{@code}:** giúp hiển thị code bên trong HTML. Các code này được hiển thị với font chữ khác các font chữ còn lại, và bạn cũng không cần lo lắng nếu có các dòng code xung đột với các tag của HTML khi này.

+ **@param:** theo sau tag này sẽ là tên tham số của hàm, theo sau nữa sẽ là lời giải thích cho tham số đó.

![_config.yml]({{ site.baseurl }}/images/javadoc-8.png)

#### @deprecated, {@link}

+ **@deprecated:** giúp đánh dấu rằng thành phần này sẽ bị gỡ bỏ khỏi API trong nay mai.

+ **{@link}:** link dẫn đến một thành phần khác trong source code.

![_config.yml]({{ site.baseurl }}/images/javadoc-9.png)

#### @exception, @throws

Hai Tag này có công dụng như nhau. Giúp thêm một thông tin Throws trong document báo hiệu phương thức này sẽ tung ra một exception.

![_config.yml]({{ site.baseurl }}/images/javadoc-10.png)

#### @return, @see

+ **@return:** giải thích cho return của hàm.

+ **@see:** đưa ra các tham khảo đến các class khác.

![_config.yml]({{ site.baseurl }}/images/javadoc-11.png)

#### {@value}

Giúp hiển thị giá trị của các static field.

![_config.yml]({{ site.baseurl }}/images/javadoc-12.png)