---
layout: post
title:  Bước ngoặc java 8
categories: Java-8
---


## Java 8

- Lambda Expressions:
    + Cho phép viết mã nguồn ngắn gọn hơn, hỗ trợ lập trình chức năng.
    + VD: 

    ```java
    List<String> names = Arrays.asList("John", "Jane", "Jack");
    names.forEach(name -> System.out.println(name));
    ```

- Stream API: 
    + Cung cấp cách xử lý tập hợp dữ liệu (collections) theo cách khai báo (declarative), giúp mã nguồn rõ ràng và dễ bảo trì hơn.
    + VD: 

    ```java
    List<String> names = Arrays.asList("John", "Jane", "Jack");
    names.stream()
     .filter(name -> name.startsWith("J"))
     .forEach(System.out::println);
    ```

- Default Methods in Interfaces
    + Cho phép thêm phương thức mặc định vào giao diện mà không phá vỡ các lớp thực hiện giao diện đó
    + VD:

    ```java
    interface MyInterface {
    default void defaultMethod() {
        System.out.println("This is a default method.");
    }
    }
    class MyClass implements MyInterface {
    // Không cần phải triển khai defaultMethod
    }

    ```

- New Date and Time API: 
    + API mới trong gói java.time giúp làm việc với ngày và giờ dễ dàng và chính xác hơn.
    + VD:

    ```java
    LocalDate date = LocalDate.now();
    LocalTime time = LocalTime.now();
    LocalDateTime dateTime = LocalDateTime.now();
    ```

- Optional Class: 
    + Giúp tránh lỗi NullPointerException bằng cách đại diện cho một giá trị có thể có hoặc không có
    + VD: 

    ```java
    Optional<String> optional = Optional.ofNullable(null);
    optional.ifPresent(System.out::println);

    ```

