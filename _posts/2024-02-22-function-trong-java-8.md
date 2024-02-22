---
layout: post
title: Function trong java 8
categories: Java-8
---
### 1. Giới thiệu Function<T,R>

Java 8 cung cấp sẵn cho chúng ta rất nhiều Functional Interface và Function<T, R> là một trong số đó. Cũng giống như những functional interface khác, Function<T, R> có thể sử dụng cho lambda expression hoặc method reference cho một mục đích cụ thể nào đó. Function<T,R> chỉ có một method trừu tượng duy nhất chấp nhận một tham số đầu vào, và method trả về một đối tượng khác.

Mục đích chính của Function là giúp chúng ta dễ dàng chuyển một đối tượng từ kiểu dữ liệu này sang kiểu dữ liệu khác.
Interface Function được khai báo trong package java.util.function như sau:

![_config.yml]({{ site.baseurl }}/images/function.png)

Trong đó:

R apply(T t) : là một phương thức trừu tượng có thể được sử dụng với lambda expression hoặc method reference cho một mục đích cụ thể nào đó.
Phương thức apply() thực hiện một hành động cụ thể trên đối số đã cho và trả về một đối khác.
Interface Function còn cung cấp một phương thức mặc định (default method) sau:

Function<V, R> compose(Function<? super V, ? extends T> before) : phương thức này trả về một function gộp mà nó đã áp dụng before Function lên đối số đầu vào, sau đó áp dụng function lên dữ liệu đầu ra.

Function<T, V> andThen(Function<? super R, ? extends V> after) : phương thức này trả về một Function gộp mà nó đã áp dụng function lên dữ liệu đầu vào và sau đó áp dụng tiếp after function lên kết quả đầu ra.

Function<T, T> identity() : phương thức này trả về một function luôn trả về đối số đầu vào của nó.


**Tham khảo:**

<ul>
    <li>
        <p>
           <https://gpcoder.com/3976-function-trong-java-8/>
        </p>
    </li>
</ul>


 