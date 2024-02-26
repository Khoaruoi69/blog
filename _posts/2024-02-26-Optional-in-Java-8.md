---
layout: post
title: Optional trong Java
categories: Java-8
---

Trong Java 8, chúng ta có một lớp Optional<T> mới được giới thiệu trong gói java.util. 

Nó được sử dụng để kiểm tra xem một biến có giá trị tồn tại giá trị hay không. Ưu điểm chính của cấu trúc mới này là không có quá nhiều kiểm tra null và tránh lỗi NullPointerException (NPE) lúc runtime. Giống như Collection và Array, Optional cũng là một vùng chứa chứa nhiều nhất một giá trị. Vùng chứa này có thể chứa bất kỳ đối tượng T nào.

### 1. Một số phương thức của Optional<T>

+ static <T> Optional<T> empty() : Trả về một Optional instance rỗng.

+ static <T> Optional<T> of(T value) : Trả về đối tượng Optional kiểu T chứa giá trị của value.

+ static <T> Optional<T> ofNullable(T value) : Trả về một Optional chứa giá trị được truyền vào nếu khác null, ngược lại sẽ trả về một Optional rỗng.

+ T get() : Nếu như có giá trị trong Optional này, nó sẽ trả về giá trị đó, ngược lại sẽ ném ra NoSuchElementException nếu như đối tượng rỗng.

+ void ifPresent(Consumer<? super T> consumer) : Nếu như đối tượng Optional đang chứa giá trị, nó sẽ áp dụng consumer được truyền vào cho giá trị của nó. Ngược lại thì không làm gì cả.

+ boolean isPresent() : Phương thức này được sử dụng để kiểm tra xem trong đối tượng Optional có đang chứa giá trị hay không. Giá trị trả về là True nếu có giá trị và ngược lại trả về false.
 orElse(T other) : Trả về giá trị của đối tượng Optional nếu có, ngược lại nó sẽ trả về đối tượng other mà bạn đã truyền vào phương thức này.

T orElseGet(Supplier<? extends T> other) : Trả về giá trị nếu tồn tại, ngược lại nó sẽ gọi other mà bạn đã truyền vào sau đó trả về kết quả của Supplier.

<U>Optional<U> map(Function<? super T,? extends U> mapper) : Nếu như có giá trị nó sẽ áp dụng Function lên giá trị đó và nếu kết quả sau khi áp dụng Function khác null thì nó sẽ trả về một Optional chứa giá trị của kết quả sau khi áp dụng Function.

<U> Optional<U> flatMap(Function<? super T,Optional<U>> mapper) : Nếu như có giá trị hiện diện trong Optional, nó sẽ áp dụng Function được truyền vào cho nó và trả về một Optional với kiểu U, ngược lại thì sẽ return về một empty Optional.

+ Optional<T> filter(Predicate<? super <T> predicate) : Nếu như có giá trị hiện diện trong đối tượng Optional này và giá trị của nó khớp với Predicate truyền vào, nó sẽ trả về một Optional chứa giá trị đó, mặc khác sẽ trả về một Optional rỗng.

+ <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) : Nếu như đối tượng Optional có giá trị tồn tại thì nó sẽ trả về giá trị đó, ngược lại sẽ ném ra một Exception do chúng ta định nghĩa bởi Supplier đã truyền vào.

### 2. Khởi taoh đối tượng Optinal<T>

**Tạo đối tượng Optinal rỗng (emtpy):**

    - Optional<String> optional = Optional.empty();

**Tạo một đối tượng chứa giá trị non-null:**

    - String str = "Khoaruoi"
    Optional<String> optional = Optional.of(str);

**Tạo một đối tượng Optional chứa giá trị cụ thể hoặc giá trị rỗng (empty) nếu tham số là null:**

    - Optional<String> optional = Optional.ofNyllable(obj);

### 3. Một vài ví dụ sử  dụng các phương thức của Optional<T>

#### 3.1 Sử dụng get(), isPresent(), ifPresent()

Kiểm tra giá trị  null trong Java-8 và trước Java 8

``` java 

2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
package com.gpcoder.optional;
 
import java.util.Optional;
 
class Student {
    String name;
}
 
public class OptionalExample1 {
 
    public void preJava8() {
        Student student = getStudent();
        if (student != null) {
            System.out.println(student.name);
        }
    }
 
    public void java8() {
        Student student = getStudent();
        Optional<Student> opt = Optional.of(student);
        if (opt.isPresent()) {
            System.out.println(opt.get().name);
        }
        // opt.ifPresent(s -> System.out.println(s.name));
    }
 
    private Student getStudent() {
        Student student = new Student();
        student.name = "gpcoder.com";
        return null;
    }
}
```

#### 3.2 Sử dụng orElse() và orElseGet()

 Hai phương thức orElse() và orElseGet() trả về giá trị của đối tượng Optional nếu nó không rỗng, nếu đối tượng rỗng thì nó trả về giá trị mặc định được truyền cho phương thức này.

**Sự khác biệt giữa orElse() và orElseGet():**

+ orElse(T other) : phương thức orElse() luôn được thực thi cho dù đối tượng Optional có rỗng hay không. Do đó, chúng ta chỉ sử dụng phương thức này khi giá trị của nó là một constant, nếu cần xử lý heavy logic để có giá trị default thì chúng ta nên sử dụng orElseGet() để có performance tốt hơn.

+ orElseGet(Supplier<? extends T> other) : tương tự như orElse(). Tuy nhiên, nó chỉ được gọi khi đối tượng Optional là rỗng.

Ví dụ chúng ta mong muốn chương trình trả về một List rỗng nếu không tồn tại giá trị. Hãy xem cách viết sử dụng phiên bản trước Java 8 và Optional của Java 8 như sau:

``` java 
package com.gpcoder.optional;
 
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
 
public class OptionalExample2 {
 
    public static void main(String[] args) {
        // Pre java 8
        List<String> list = getList();
        List<String> listOpt = list != null ? list : new ArrayList<>();
 
        // Java 8
        List<String> listOpt2 = getList2().orElse(new ArrayList<>());
        List<String> listOpt3 = getList2().orElseGet(() -> new ArrayList<>());
    }
 
    private static List<String> getList() {
        return null;
    }
 
    private static Optional<List<String>> getList2() {
        return Optional.ofNullable(null);
    }
}
```

#### 3.3 Sử dụng map()

Phương thức map() của Optional: Nếu có một giá trị, áp dụng hàm ánh xạ được cung cấp cho nó. Nếu kết quả != null, trả về một Optional với kết quả. Ngược lại thì trả về một Optional rỗng.

``` java 
package com.gpcoder.optional;
 
import java.util.Optional;
 
class User {
 
    private Address address;
 
    public Address getAddress() {
        return address;
    }
}
 
class Address {
 
    private String street;
 
    public String getStreet() {
        return street;
    }
}
 
public class OptionalExample3 {
 
    public String getStreetPreJava8() {
        User user = getUser();
        if (user != null) {
            Address address = user.getAddress();
            if (address != null) {
                String street = address.getStreet();
                if (street != null) {
                    return street;
                }
            }
        }
        return "not specified";
    }
 
    public String getStreetJava8() {
        Optional<User> user = Optional.ofNullable(getUser());
        return user.map(User::getAddress)
                .map(Address::getStreet)
                .orElse("not specified");
    }
 
    private static User getUser() {
        return null;
    }
}
```

Trong ví dụ này, tôi sử dụng phương thức map() để lấy kết quả getAdress () từ Optional<Address> và getStreet() từ Optional<String>. Nếu bất kỳ phương thức nào trong số này trả về null thì phương thức map() sẽ trả về một Optional rỗng.

#### 3.4 Sử dụng map() và flatMap()

Sử dụng phương thức map() có thể tồn tại các Optional lồng nhau. Sử dụng phương thức flatMap(), các Optional lồng nhau sẽ được gộp (flat) lại một Optional duy nhất.

``` java 
package com.gpcoder.optional;
 
import java.util.Optional;
 
public class OptionalExample4 {
 
    public static void main(String[] args) {
        Optional<String> optionalOf = Optional.of("welcome to gpcoder.com");
        Optional<String> optionalEmpty = Optional.empty();
 
        System.out.println(optionalOf.map(String::toLowerCase));
        System.out.println(optionalEmpty.map(String::toLowerCase));
 
        Optional<optional<String>> multiOptional = Optional.of(Optional.of("gpcodre"));
 
        System.out.println("Value of Optional object: " + multiOptional);
        System.out.println("Optional.map: " + multiOptional.map(gender -> gender.map(String::toUpperCase)));
        System.out.println("Optional.flatMap: " + multiOptional.flatMap(gender -> gender.map(String::toUpperCase)));
    }
}
```

Output của chương trình trên:

    - Optional[welcome to gpcoder.com]
      Optional.empty
      Value of Optional object: Optional[Optional[gpcoder]]
      Optional.map: Optional[Optional[GPCODER]]
      Optional.flatMap: Optional[GPCODER]

Như bạn thấy Optional<Optional<String>> khi sử dụng flatMap() kết quả sẽ là Optional<String>, còn map() vẫn là Optional<Optional<String>>.

#### 3.5 Sử dụng filter()

Phương thức **filter()** : Nếu như có giá trị hiện diện trong đối tượng Optional này và giá trị của nó khớp với **Predicate** truyền vào, nó sẽ trả về một Optional chứa giá trị đó, mặc khác sẽ trả về một Optional rỗng.

``` java 
package com.gpcoder.optional;
 
import java.util.Optional;
 
public class OptionalExample5 {
 
    public static void main(String[] args) {
        Optional<String> me = Optional.of("gpcoder");
        Optional<String> emptyOptional = Optional.empty();
 
        //Filter on Optional
        System.out.println(me.filter(g -> g.equals("GPCODER"))); //Optional.empty
        System.out.println(me.filter(g -> g.equalsIgnoreCase("gpcoder"))); //Optional[gpcoder]
        System.out.println(emptyOptional.filter(g -> g.equalsIgnoreCase("gpcoder"))); //Optional.empty
    }
}
```

#### 3.6 Sử dụng orElseThrow()

Phương thức orElseThrow() : Nếu như đối tượng Optional có giá trị tồn tại thì nó sẽ trả về giá trị đó, ngược lại sẽ ném ra một Exception do chúng ta định nghĩa bởi Supplier đã truyền vào.

``` java
package com.gpcoder.optional;
 
import java.util.Optional;
 
public class OptionalExample6 {
 
    public static void main(String[] args) {
        Optional<String> shouldNotBeEmpty = Optional.empty();
        shouldNotBeEmpty.orElseThrow(() -> new IllegalStateException("This should not happen!!!"));
    }
}
```

Output của chương trình trên:

    - Exception in thread "main" java.lang.IllegalStateException: Data is not found

Tóm lại, Optional là một tính năng mới rất tiện lợi trong Java 8, được tạo riêng để quản lý các giá trị còn thiếu. Nó là một lớp bao bọc chứa một thể hiện của lớp đã có giá trị hoặc một giá trị null. Do đó, có thể thao tác các giá trị null như thể chúng là các thể hiện bình thường mà không nhất thiết phải thực hiện kiểm tra null ở mỗi bước.

Sử dụng Optional giúp chúng ta giảm bớt được các câu lệnh if-else không cần thiết, tránh lỗi Null Pointer Exception, nói cho người sử dụng biết là một value liệu có thể null hay không.