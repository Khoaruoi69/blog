---
layout: post
title: Predicate trong java 8
categories: Java-8
---

### 1. Giới thiệu về Predicate<T>

Trong Java 8, Predicate<T> là một functional interface và do đó nó có thể được sử dụng với lambda expression hoặc method reference cho một mục đích cụ thể nào đó. Predicate<T> sẽ trả về giá trị true/false của một tham số kiểu T mà bạn đưa vào có thỏa với điều kiện của Predicate đó hay không, cụ thể là điều kiện được viết trong phương thức test().

Interface Predicate được khai báo trong package java.util.function như sau:

![_config.yml]({{ site.baseurl }}/images/predicate.png)

Trong đó:

+ **boolean test(T t) :** là một phương thức trừu tượng có thể được sử dụng với lambda expression hoặc method reference cho một mục đích cụ thể nào đó.
Phương thức test() trả về true nếu đối số đầu vào khớp với biến predicate (điều kiện kiểm tra), nếu không trả về false.

**Một số phương thức mặc định (default method) trong lớp Interface Predicate:**

+ **and() :** Nó thực hiện logic AND của predicate mà nó được gọi với một biến predicate khác. Ví dụ: predicate1.and (predicate2).

+ **or() :** Nó thực hiện logic OR của predicate mà nó được gọi với một biến predicate khác. Ví dụ: predicate1.or(predicate2).

+ **negate() :** Nó thực hiện phủ định kết quả của biến predicate được gọi. Ví dụ: predicate1.negate().

Ngoài ra interface Predicate còn có một phương thức tĩnh (static method) là isEqual(). Phương thức này được sử dụng để kiểm tra hai đối có số bằng nhau theo Objects.equals (Object, Object).

### 2. Ví dụ

#### 2.1 Sử dụng test()

``` java 
package com.gpcoder.predicate;
 
import java.util.function.Predicate;
 
public class PredicateExample1 {
 
    public static void main(String[] args) {
 
        // Predicate String
        Predicate<String> predicateString = s -> {
            return s.equals("gpcoder");
        };
        System.out.println(predicateString.test("gpcoder")); // true
        System.out.println(predicateString.test("GP Coder")); // false
 
        // Predicate integer
        Predicate<Integer> predicateInt = i -> {
            return i > 0;
        };
        System.out.println(predicateInt.test(1)); // true
        System.out.println(predicateInt.test(-1)); // false
    }
}
```

#### 2.2 Sử dụng and(), or(), negate(), isEqual()

``` java
package com.gpcoder.predicate;
 
import java.util.function.Predicate;
 
public class PredicateExample2 {
 
    public static void main(String[] args) {
 
        Predicate<String> predicate = s -> {
            return s.equals("gpcoder");
        };
 
        // AND logical operation
        Predicate<String> predicateAnd = predicate.and(s -> s.length() == 11);
        System.out.println(predicateAnd.test("gpcoder.com")); // false
 
        // OR logical operation
        Predicate<String> predicateOr = predicate.or(s -> s.length() == 11);
        System.out.println(predicateOr.test("gpcoder.com")); // true
 
        // NEGATE logical operation
        Predicate<String> predicateNegate = predicate.negate();
        System.out.println(predicateNegate.test("gpcoder")); // false
    }
}

```

#### 2.3 Kết hợp nhiều Predicate

``` java
package com.gpcoder.predicate;
 
import java.util.function.Predicate;
 
public class PredicateExample3 {
 
    public static void main(String[] args) {
 
        // Creating predicate
        Predicate<Integer> greaterThanTen = (i) -> i > 10;
        Predicate<Integer> lessThanTwenty = (i) -> i < 20;
 
        // Calling Predicate Chaining
        boolean result = greaterThanTen.and(lessThanTwenty).test(15);
        System.out.println(result); // true
 
        // Calling Predicate method
        boolean result2 = greaterThanTen.and(lessThanTwenty).negate().test(15);
        System.out.println(result2); // false
    }
}
```

#### 2.4 Sử dụng Predicate với Collection

Ví dụ chúng ta có thông tin Employee bao gồm các thông tin id, tên, tuổi, giới tính, … như bên dưới:

```java
package com.gpcoder.predicate;
 
public class Employee {
 
    private Integer id;
    private Integer age;
    private String gender;
    private String firstName;
    private String lastName;
 
    public Employee(Integer id, Integer age, String gender, String fName, String lName) {
        this.id = id;
        this.age = age;
        this.gender = gender;
        this.firstName = fName;
        this.lastName = lName;
    }
 
    @Override
    public String toString() {
        return this.id.toString() + " - " + this.age.toString();
    }
 
    // Please generate Getter and Setters
}
```

Bây giờ chúng ta sẽ xây dựng các Predicate để thực thi như sau:

```java
package com.gpcoder.predicate;
 
import java.util.List;
import java.util.function.Predicate;
import java.util.stream.Collectors;
 
public class EmployeePredicates {
 
    // Tất cả Employee có tuổi > 21 và có giới tính là Male
    public static Predicate<Employee> isAdultMale() {
        return p -> p.getAge() > 21 && p.getGender().equalsIgnoreCase("M");
    }
 
    // Tất cả Employee có tuổi > 18 và có giới tính là Female
    public static Predicate<Employee> isAdultFemale() {
        return p -> p.getAge() > 18 && p.getGender().equalsIgnoreCase("F");
    }
 
    // Tất cả Employee lớn hơn số tuổi được truyền vào
    public static Predicate<Employee> isAgeMoreThan(Integer age) {
        return p -> p.getAge() > age;
    }
 
    // Lấy danh sách Employee thõa mãn predicate được truyền vào
    public static List<Employee> filterEmployees( //
            List<Employee> employees, Predicate<Employee> predicate) {
        return employees.stream().filter(predicate).collect(Collectors.<Employee>toList());
    }
}
```

Chúng ta sẽ bắt đầu apply các Predicate này như sau:

``` java
package com.gpcoder.predicate;
 
import static com.gpcoder.predicate.EmployeePredicates.filterEmployees;
import static com.gpcoder.predicate.EmployeePredicates.isAdultFemale;
import static com.gpcoder.predicate.EmployeePredicates.isAdultMale;
import static com.gpcoder.predicate.EmployeePredicates.isAgeMoreThan;
 
import java.util.Arrays;
import java.util.List;
 
public class EmployeePredicatesTest {
 
    public static void main(String[] args) {
 
        List<Employee> employees = Arrays.asList( //
                new Employee(1, 23, "M", "Rick", "Beethovan"), //
                new Employee(2, 13, "F", "Martina", "Hengis"), //
                new Employee(3, 43, "M", "Ricky", "Martin"), //
                new Employee(4, 26, "M", "Jon", "Lowman"), //
                new Employee(5, 19, "F", "Cristine", "Maria"), //
                new Employee(6, 15, "M", "David", "Feezor"), //
                new Employee(7, 68, "F", "Melissa", "Roy"), //
                new Employee(8, 79, "M", "Alex", "Gussin"), //
                new Employee(9, 15, "F", "Neetu", "Singh"), //
                new Employee(10, 45, "M", "Naveen", "Jain") //
        );
 
        // Lấy danh sách Employee nam 21+
        System.out.println(filterEmployees(employees, isAdultMale()));
 
        // Lấy danh sách Employee nữ 18+
        System.out.println(filterEmployees(employees, isAdultFemale()));
 
        // Lấy danh sách Employee > 35 tuổi
        System.out.println(filterEmployees(employees, isAgeMoreThan(35)));
 
        // Lấy danh sách Employee <= 35 tuổi
        System.out.println(filterEmployees(employees, isAgeMoreThan(35).negate()));
    }
}
 ```

 Output của chương trình trên như sau:

    - [1 - 23, 3 - 43, 4 - 26, 8 - 79, 10 - 45]
      [5 - 19, 7 - 68]
      [3 - 43, 7 - 68, 8 - 79, 10 - 45]
      [1 - 23, 2 - 13, 4 - 26, 5 - 19, 6 - 15, 9 - 15]


#### 2.5 Sử dụng Predicate với các lớp cho kiểu dữ liệu nguyên thủy (primitive type)

Java 8 cung cấp một số Interface Predicate cho các wrapper class của kiểu dữ liệu nguyên thủy như sau:

+ IntPredicate : chấp nhận một giá trị kiểu Int duy nhất.

+ LongPredicate : chấp nhận một giá trị kiểu Long duy nhất.

+ DoublePredicate : chấp nhận một giá trị kiểu Double duy nhất.

Ví dụ:

``` java 
package com.gpcoder.predicate;
 
import java.util.Arrays;
import java.util.function.DoublePredicate;
import java.util.function.IntPredicate;
import java.util.function.LongPredicate;
 
public class PredicateExample4 {
 
    public static void main(String[] args) {
 
        System.out.print("IntPredicate: ");
        int[] intNumbers = { 3, 5, 6, 2, 1 };
        IntPredicate intPredicate = i -> i > 5;
        Arrays.stream(intNumbers).filter(intPredicate).forEach(System.out::println);
 
        System.out.print("\nLongPredicate: ");
        long[] longNumbers = { 3, 5, 6, 2, 1 };
        LongPredicate longPredicate = l -> l > 5;
        Arrays.stream(longNumbers).filter(longPredicate).forEach(System.out::println);
 
        System.out.print("\nDoublePredicate: ");
        double[] dbNumbers = { 3.2, 5.0, 6.3, 2.5, 1.0 };
        DoublePredicate dbPredicate = d -> d > 5;
        Arrays.stream(dbNumbers).filter(dbPredicate).forEach(System.out::println);
    }
}
```
Output của chương trình trên:

    - IntPredicate: 6
      LongPredicate: 6
      DoublePredicate: 6.3

#### 2.6 Sử dụng Predicate 2 đối số với BiPredicate

Như đã nói ở phần trên Predicate chỉ chấp nhận 1 đối số đầu vào và trả về 1 giá trị true/false, để có thể sử dụng Predicate với 2 đối số đầu vào chúng ta sử dụng Interface BiPredicate:

![_config.yml]({{ site.baseurl }}/images/BiPredicate.png)

Về cơ bản, interface BiPredicate không khác biệt so với Predicate, ngoại trừ nó chấp nhận 2 đối số đầu vào

``` java 
package com.gpcoder.predicate;
 
import java.util.function.BiPredicate;
 
public class BiPredicateExample {
 
    public static void main(String[] args) {
 
        BiPredicate<Integer, String> condition = (i, s) -> i > 2 &amp;&amp; s.startsWith("J");
        System.out.println(condition.test(5, "Java")); // true
        System.out.println(condition.test(2, "Javascript")); // false
        System.out.println(condition.test(1, "C#")); // false
    }
}
```
### Tham khảo: 

*[Predicate trong java 8](https://gpcoder.com/3963-predicate-trong-java-8/)*