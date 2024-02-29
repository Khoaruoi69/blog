---
layout: post
title: Một số lý thuyết quan trọng trong java 8
categories: Java-8
---

## Mộ số lý thuyết trong java 8:

### 1.Supplier

**Supplier:**  là một **functional interface** và do đó nó có thể được sử dụng với **lambda expression** hoặc **method reference** cho một mục đích cụ thể nào đó.
 Supplier<T> làm ngược lại với Consumer<T>, nó là một phương thức trừu tượng không tham số, và trả về một đối tượng bằng cách gọi phương thức get() của nó.

Mục đích chính của Interface này là cung cấp một cái gì đó cho chúng ta khi cần.

**Tài liệu tham khảo:**

*[Supplier trong java 8](https://gpcoder.com/3967-supplier-trong-java-8/)*

### 2. Consumer
**Consumer:** là một **functional interface** và do đó nó có thể được sử dụng với **lambda expression** hoặc **method reference** cho một mục đích cụ thể nào đó. 
Consumer<T> chấp nhận một tham số đầu vào, và method này không trả về gì cả.

Mục tiêu chính của Interface Consumer là thực hiện một thao tác trên đối số đã cho.

**Tài liệu tham khảo:**

*[Consumer trong java 8](https://gpcoder.com/3965-consumer-trong-java-8/)*

*ví dụ về cách sử dụng Consumer làm callback function trong Java 8:*

**Giả sử bạn có một lớp Calculator với một phương thức tính tổng của hai số và một phương thức tính tổng của một mảng số:**

``` java 
import java.util.function.Consumer;

public class Calculator {
    public static void sum(int a, int b, Consumer<Integer> callback) {
        int result = a + b;
        callback.accept(result);
    }

    public static void sumArray(int[] numbers, Consumer<Integer> callback) {
        int sum = 0;
        for (int number : numbers) {
            sum += number;
        }
        callback.accept(sum);
    }
}
```
**Sau đó, bạn có thể sử dụng các phương thức này với Consumer như sau:**

```java
public class Main {
    public static void main(String[] args) {
        // Sử dụng Consumer để in ra kết quả
        Consumer<Integer> printResult = (result) -> System.out.println("Result: " + result);

        // Tính tổng của hai số và sử dụng Consumer để in ra kết quả
        Calculator.sum(5, 3, printResult);

        // Tính tổng của một mảng số và sử dụng Consumer để in ra kết quả
        int[] numbers = {1, 2, 3, 4, 5};
        Calculator.sumArray(numbers, printResult);
    }
}
```

Trong ví dụ này, chúng ta đã tạo một Consumer được gọi là printResult, nó nhận một số nguyên và in ra kết quả.
 Sau đó, chúng ta đã sử dụng Consumer này như là một callback function trong hai phương thức của lớp Calculator.
  Khi các phương thức sum và sumArray được gọi, chúng ta truyền printResult như một đối số thứ ba. Khi kết quả được tính toán xong, Consumer này được gọi để in ra kết quả.

### 3. Predicate

**Predicate:**  là một functional interface và do đó nó có thể được sử dụng với lambda expression hoặc method reference cho một mục đích cụ thể nào đó.
 Predicate<T> sẽ trả về giá trị true/false của một tham số kiểu T mà bạn đưa vào có thỏa với điều kiện của Predicate đó hay không, cụ thể là điều kiện được viết trong phương thức test().

**Tài liệu tham khảo:**

*[Predicate trong java 8](https://gpcoder.com/3963-predicate-trong-java-8/)*

### 4. Function

**Function:** Java 8 cung cấp sẵn cho chúng ta rất nhiều Functional Interface và Function<T, R> là một trong số đó. 
Cũng giống như những functional interface khác, Function<T, R> có thể sử dụng cho lambda expression hoặc method reference cho một mục đích cụ thể nào đó.
 Function<T,R> chỉ có một method trừu tượng duy nhất chấp nhận một tham số đầu vào, và method trả về một đối tượng khác.

Mục đích chính của Function là giúp chúng ta dễ dàng chuyển một đối tượng từ kiểu dữ liệu này sang kiểu dữ liệu khác

**Tài liệu tham khảo:**

*[Function trong java 8](https://gpcoder.com/3976-function-trong-java-8/)*

*ví dụ cơ bản*

``` java
public class Main {
    public static void main(String[] args) {
        // Khai báo một function để nhân một số cho 2
        Function<Integer, Integer> multiplyByTwo = x -> x * 2;

        // Sử dụng function để nhân một số cho 2
        int result = multiplyByTwo.apply(5);
        System.out.println("5 nhân 2 là: " + result);

        // Khai báo một function để thêm dấu chấm sau mỗi chuỗi
        Function<String, String> addDot = s -> s + ".";

        // Sử dụng function để thêm dấu chấm sau chuỗi "Hello"
        String text = addDot.apply("Hello");
        System.out.println("Chuỗi sau khi thêm dấu chấm: " + text);
    }
}
```
### 5. Collectors

**Stream.collect()**  là một trong các phương thức đầu cuối (terminal operation) của Stream API trong Java 8. Nó cho phép thực hiện các thao tác có thể thay đổi trên các phần tử được lưu giữ trong Stream. Chẳng hạn như: chuyển các phần tử sang một số cấu trúc dữ liệu khác, áp dụng một số logic bổ sung, tính toán, …

Lớp Java Collectors cung cấp nhiều phương thức khác nhau để xử lý các phần tử của Stream API. Chẳng hạn như:

+ Collectors.toList()
+ Collectos.toSet()
+ Collectors.toMap() ...

**Tài liệu tham khảo:**

*[Lớp Collectors trong java 8](https://gpcoder.com/3973-lop-collectors-trong-java-8/)*

*ví dụ cơ bản:*

``` java
import java.util.*;
import java.util.stream.Collectors;

public class Main {
    public static void main(String[] args) {
        // Tạo một danh sách các sinh viên
        List<Student> students = Arrays.asList(
                new Student(1, "Alice"),
                new Student(2, "Bob"),
                new Student(3, "Charlie"),
                new Student(4, "David")
        );

        // Tạo một Map với key là ID và value là tên của sinh viên
        Map<Integer, String> idToNameMap = students.stream()
                .collect(Collectors.toMap(Student::getId, Student::getName));

        // In ra map
        idToNameMap.forEach((id, name) -> System.out.println("ID: " + id + ", Name: " + name));
    }

    // Class Student
    static class Student {
        private int id;
        private String name;

        public Student(int id, String name) {
            this.id = id;
            this.name = name;
        }

        public int getId() {
            return id;
        }

        public String getName() {
            return name;
        }
    }
}

```

****

****

****
