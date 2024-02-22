---
layout: post
title: Abstract class và Interface trong Java
categories: OOP
---

### 1. Lớp trừu tượng (Abstarct Class) trong Java

#### 1.1 Đặc điểm của lớp trừu tượng (Abstract class)

Một lớp được khai báo với từ khóa **abstract** là lớp trừu tượng (abstract class).

Lớp trừu tượng có thể có các phương thức abstract hoặc non-abtract.

Lớp trừu tượng có thể khai báo 0, 1 hoặc nhiều method trừu tượng bên trong.

Không thể khởi tạo 1 đối tượng trực tiếp từ một class trừu tượng.

Một lớp kế thừa từ lớp trừu tượng (subclass – lớp con) không cần phải implement non-abstract methods, nhưng những method nào có abstract thì bắt buộc phải override. Trừ khi subclass cũng là abstract.

**Cú pháp:**

    - <PhamViTruyCap> abstract class <TenLop> {}

#### 1.2 Phương thức trừu tượng trong Java

Một phương thức được khai báo là abstract và không có trình triển khai thì đó là phương thức trừu tượng (abstract method).

Nếu bạn muốn một lớp chứa một phương thức cụ thể nhưng bạn muốn triển khai thực sự phương thức đó để được quyết định bởi các lớp con, thì bạn có thể khai báo phương thức đó trong lớp cha ở dạng abstract.

Từ khóa abstract được sử dụng để khai báo một phương thức dạng abstract. Phương thức abstract sẽ không có định nghĩa, được theo sau bởi dấu chấm phẩy, không có dấu ngoặc nhọn theo sau.

**Cú pháp:**

    - <PhamViTruyCap> abstract void <TenPhuongThuc>();

#### 1.3 Một số lưu ý 

**Lớp con bắt buộc phải cài đặt (implement) tất cả các phương thức trừu tượng của lớp cha**

**Không thể khởi tạo trực tiếp một lớp trừu tượng**

*VD:* 
```java 
    abstract class Animal {
    // Phương thức trừu tượng
    public abstract void makeSound();

    // Phương thức không trừu tượng
    public void sleep() {
        System.out.println("Zzz");
    }
}

class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Woof!");
    }
}

class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Meow!");
    }
}

public class Main {
    public static void main(String[] args) {
        // Không thể khởi tạo một đối tượng từ lớp trừu tượng Animal
        // Animal animal = new Animal(); // Lỗi biên dịch: Cannot instantiate the type Animal
        
        // Nhưng bạn có thể khởi tạo đối tượng từ một lớp con của lớp trừu tượng
        Animal dog = new Dog();
        dog.makeSound(); // In ra: Woof

        Dog dog = new Dog();
        dog.makeSound(); // In ra: Woof!
        dog.sleep();     // In ra: Zzz

        Cat cat = new Cat();
        cat.makeSound(); // In ra: Meow!
        cat.sleep();     // In ra: Zzz
    }
}
```

**Sự khác nhau giữa abstract và non-abstract**

 Phương thức abstract và non-abstract (còn được gọi là concrete method) là hai loại phương thức khác nhau trong lập trình hướng đối tượng. Dưới đây là sự khác biệt chính giữa chúng:

*A.Phương thức abstract:*

Một phương thức abstract là một phương thức trong một lớp trừu tượng mà không có cài đặt (không có body).
Phải được định nghĩa trong lớp trừu tượng bằng cách sử dụng từ khóa abstract.

Lớp chứa phương thức abstract phải là lớp trừu tượng.

Phải được triển khai (override) bởi lớp con của lớp trừu tượng đó.

Mục đích của phương thức abstract là để bắt buộc các lớp con cung cấp một cài đặt cho phương thức đó.

*B.Phương thức non-abstract (concrete method):*

Một phương thức non-abstract là một phương thức có cài đặt đầy đủ (body) trong lớp.

Không có từ khóa abstract được sử dụng để định nghĩa phương thức non-abstract.

Cung cấp một cài đặt cụ thể cho phương thức.

Các lớp con có thể sử dụng phương thức non-abstract mà không cần phải triển khai lại (override) nếu chúng muốn.

### 2. Interface trong Java

**Đặc điểm của Interface:**

+ Các phương thức trong interface đều là các phương thức trừu tượng.
+ Interface là một kỹ thuật để thu được tính trừu tượng hoàn toàn và đa kế thừa trong Java.
+ Interface luôn có modifier là: **public interface** dù bạn có khai báo rõ hay không
+ Nếu có các trường (field) thì chúng đều là: **public static final** cho dù bạn khai báo rõ hay không
+ Các method của nó đều là method trừu tượng, nghĩa là không có thân hàm, và đều có modifier là: **public abstract**, cho dù bạn có khai báo hay không.
+ Interface **không có hàm khởi tạo (constructor)**
+ Một interface không phải là một lớp. Viết một interface giống như viết một lớp, nhưng chúng có 2 định nghĩa khác nhau. Một lớp mô tả các thuộc tính và hành vi của một đối tượng. Một interface chứa các hành vi mà một class triển khai.
+ Trừ khi một lớp triển khai interface là lớp trừu tượng abstract, còn lại tất cả các phương thức của interface cần được định nghĩa trong class.

*Java Compiler thêm từ khóa public abstract trước phương thức của interface và các từ khóa public static final trước các thành viên dữ liệu.*

**Một interface tương tự với một class bởi những điểm sau đây:**

+ Một interface được viết trong một file với định dạng .java, với tên của interface giống tên của file.

+ Bytecode của interface được lưu trong file có định dạng .class.

+ Khai báo interface trong một package, những file bytecode tương ứng cũng có cấu trúc thư mục có cùng tên package.

**Một interface khác với một class ở một số điểm sau đây:**

+ Bạn không thể khởi tạo một interface.
+ Một interface không chứa bất cứ hàm Contructor nào.
+ Tất cả các phương thức của interface đều là abstract.
+ Một interface không thể chứa một trường nào trừ các trường vừa static và final.
+ Một interface không thể kế thừa từ lớp, nó được triển khai bởi một lớp.
+ Một interface có thể kế thừa từ nhiều interface khác.

**Giải thích cụ thể hơn:**

**1.Interface không phải là một lớp:**

Interface không thể được xem như một lớp trong Java vì nó không có khả năng định nghĩa trực tiếp các thuộc tính hoặc cài đặt của các phương thức.

Interface chỉ định nghĩa các phương thức mà một lớp cụ thể sẽ triển khai (implements).

Interface không thể được khởi tạo để tạo ra đối tượng, nhưng nó có thể được sử dụng để khai báo tham chiếu đến một đối tượng.

**2.Viết một interface giống như viết một lớp, nhưng chúng có 2 định nghĩa khác nhau:**

Khi bạn viết một interface trong Java, bạn đang xác định các hành vi mà các lớp sẽ triển khai.

Tương tự như viết một lớp, bạn sẽ định nghĩa các phương thức trong interface, nhưng chúng chỉ được định nghĩa mà không có cài đặt.

Trong khi đó, khi bạn viết một lớp, bạn có thể định nghĩa cả các thuộc tính và cài đặt cụ thể của các phương thức.

**3. Một lớp mô tả các thuộc tính và hành vi của một đối tượng:**

Lớp trong Java mô tả các đối tượng bằng cách xác định các thuộc tính và hành vi của chúng.

Các thuộc tính đại diện cho trạng thái của đối tượng, trong khi các phương thức đại diện cho hành vi của đối tượng.

Ví dụ: Một lớp Car có thể có các thuộc tính như màu sắc, hãng sản xuất, năm sản xuất, và các phương thức như start(), accelerate(), và brake().

**4.Một interface chứa các hành vi mà một class triển khai:**

Interface chỉ định nghĩa các phương thức mà các lớp cụ thể (class) sẽ triển khai.

Các lớp triển khai (implements) interface phải cung cấp cài đặt cho tất cả các phương thức trong interface đó.

Ví dụ: Interface Drawable có thể chứa phương thức draw(). Một lớp Circle có thể triển khai (implements) interface Drawable và cung cấp cài đặt cho phương thức draw(), trong khi một lớp khác như Square cũng có thể triển khai interface Drawable và cung cấp một cài đặt khác cho phương thức draw().

*VD:*
``` java 
// Định nghĩa một interface có tên là Shape
interface Shape {
    // Phương thức để tính diện tích
    double calculateArea();

    // Phương thức để tính chu vi
    double calculatePerimeter();
}

// Lớp Rectangle triển khai (implements) interface Shape
class Rectangle implements Shape {
    private double length;
    private double width;

    // Constructor
    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    // Override phương thức tính diện tích từ interface
    @Override
    public double calculateArea() {
        return length * width;
    }

    // Override phương thức tính chu vi từ interface
    @Override
    public double calculatePerimeter() {
        return 2 * (length + width);
    }
}

// Lớp Circle triển khai (implements) interface Shape
class Circle implements Shape {
    private double radius;

    // Constructor
    public Circle(double radius) {
        this.radius = radius;
    }

    // Override phương thức tính diện tích từ interface
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }

    // Override phương thức tính chu vi từ interface
    @Override
    public double calculatePerimeter() {
        return 2 * Math.PI * radius;
    }
}

// Main class để kiểm tra
public class Main {
    public static void main(String[] args) {
        // Tạo một hình chữ nhật
        Shape rectangle = new Rectangle(5, 3);
        System.out.println("Rectangle Area: " + rectangle.calculateArea());
        System.out.println("Rectangle Perimeter: " + rectangle.calculatePerimeter());

        // Tạo một hình tròn
        Shape circle = new Circle(4);
        System.out.println("Circle Area: " + circle.calculateArea());
        System.out.println("Circle Circumference: " + circle.calculatePerimeter());
    }
}
```

**Lưu ý:**
*Khi ghi đè các phương thức được định nghĩa trong interface, có một số qui tắc sau:*

+ Các checked exception không nên được khai báo trong phương thức implements, thay vào đó nó nên được khai báo trong phương thức interface hoặc các lớp phụ được khai báo bởi phương thức interface.

+ Signature (ký số) của phương thức interface và kiểu trả về nên được duy trì khi ghi đè phương thức (overriding method).

+ Một lớp triển khai chính nó có thể là abstract và vì thế các phương thức interface không cần được triển khai.

*VD:*
``` java 
// Định nghĩa interface
interface Printable {
    void print();
}

// Lớp trừu tượng triển khai interface và cũng là một lớp trừu tượng
abstract class AbstractPrintable implements Printable {
    // Phương thức abstract từ interface, không cần triển khai
}

// Lớp con kế thừa từ lớp trừu tượng và triển khai phương thức cụ thể
class Printer extends AbstractPrintable {
    @Override
    public void print() {
        System.out.println("Printing...");
    }
}

public class Main {
    public static void main(String[] args) {
        Printer printer = new Printer();
        printer.print(); // In ra: Printing...
    }
}
```

*Khi triển khai interface, có vài quy tắc sau:*

+ Một lớp có thể triển khai một hoặc nhiều interface tại một thời điểm.

+ Một lớp chỉ có thể kế thừa một lớp khác, nhưng được triển khai nhiều interface.

+ Một interface có thể kế thừa từ một interface khác, tương tự cách một lớp có thể kế thừa lớp khác.

### 2.2 Đa kế thừa trong Java bởi Interface

Nếu một lớp triển khai đa kế thừa, hoặc một Interface kế thừa từ nhiều Interface thì đó là đa kế thừa.

Trong Java, một lớp chỉ được thừa kế (extends) từ một lớp, có thể cài đặt (implements) nhiều interface. Tuy nhiên, một interface có thể thừa kế (extends) nhiều interface.

Một interface không thể cài đặt (implements) interface khác, do interface không phần cài đặt, chỉ chứa các khai báo.

*VD:*
**Ví dụ một lớp cài đặt (implements) nhiều interface:**

```java
public interface Shape {    
    void draw();    
}
 
public interface Color {
    String getColor();
}
 
public class Rectangle implements Shape, Color {
 
    @Override
    public void draw() {
        System.out.println("Draw " + this.getColor() + " rectangle");
    }
 
    @Override
    public String getColor() {
        return "red";
    }
     
}
```

**Ví dụ interface kế thừa (extend) nhiều interface:**

``` java
public interface Shape {    
    void draw();    
}
 
public interface Color {
    String getColor();
}
 
public interface ShapeColor extends Shape, Color {
 
}
 
public class Circle implements ShapeColor {
 
    @Override
    public void draw() {
        System.out.println("Draw " + this.getColor() + " circle");
    }
 
    @Override
    public String getColor() {
        return "red";
    }
     
}
```
### 2.3 Marker (hay Tagging) Interface trong Java là gì ?

Đó là một Interface mà không có thành viên nào. Ví dụ: Serializable, Cloneable, Remote, … Chúng được sử dụng để cung cấp một số thông tin thiết yếu tới JVM để mà JVM có thể thực hiện một số hoạt động hữu ích.

*VD:*

    - public interface Serializable {}

**Có hai mục đích thiết kế chủ yếu của tagging interface là:**

+ Tạo một cha chung: Như với EventListener interface, mà được kế thừa bởi hàng tá các interface khác trong Java API, bạn có thể sử dụng một tagging interface để tạo một cha chung cho một nhóm interface. Ví dụ, khi một interface kế thừa EventListener, thì JVM biết rằng interface cụ thể này đang được sử dụng trong một event.

+ Thêm một kiểu dữ liệu tới một class: Đó là khái niệm tagging. Một class mà triển khai một tagging interface không cần định nghĩa bất kỳ phương thức nào, nhưng class trở thành một kiểu interface thông qua tính đa hình (polymorphism).

*Giải thích cụ thể*

rong Java, một "Marker Interface" hay "Tagging Interface" là một interface không chứa bất kỳ phương thức nào. Chúng chỉ đơn giản là đánh dấu (marker) một loạt các lớp để chỉ ra rằng các lớp đó có một tính chất hoặc một loại đặc biệt.

Cụ thể, khi một lớp triển khai một marker interface, nó chỉ ra rằng nó có một đặc điểm hoặc tính chất nào đó mà interface đó đại diện cho. Điều này có thể giúp cho việc phân loại hoặc xử lý các đối tượng dễ dàng hơn trong quá trình thực thi.

Ví dụ phổ biến nhất của marker interface trong Java là Serializable. Interface này không có bất kỳ phương thức nào, nhưng nó chỉ đơn giản là một cách đánh dấu rằng các đối tượng của các lớp triển khai nó có thể được tuần tự hóa (serialize) để có thể truyền đi qua mạng hoặc lưu trữ vào bộ nhớ ngoài.

Dưới đây là một ví dụ về cách sử dụng marker interface trong Java:
``` java
import java.io.Serializable;

// Marker interface
interface Marker {
    // Không có phương thức
}

// Lớp triển khai Marker interface
class MyClass implements Marker, Serializable {
    // Phương thức và thuộc tính của lớp
}

public class Main {
    public static void main(String[] args) {
        MyClass obj = new MyClass();

        // Kiểm tra xem obj có triển khai Marker interface không
        if (obj instanceof Marker) {
            System.out.println("obj implements Marker interface");
        } else {
            System.out.println("obj does not implement Marker interface");
        }

        // Kiểm tra xem obj có triển khai Serializable interface không
        if (obj instanceof Serializable) {
            System.out.println("obj implements Serializable interface");
        } else {
            System.out.println("obj does not implement Serializable interface");
        }
    }
}

```

### 3. So sánh abstract class và interface trong Java

**Lớp trừu tượng( abstract class)**

Thể hiện tính trừu tượng < 100%

Lớp trừu tượng có thể có các phương thức abstract và non-abstract

Lớp trừu tượng không hỗ trợ đa kế thừa

Lớp trừu tượng có thể có các biến final, non-final, static và non-static

Lớp trừu tượng có thể có phương thức static, phương thức main và constructor

Từ khóa abstract được sử dụng để khai báo lớp trừu tượng

Lớp trừu tượng có thể cung cấp trình triển khai của Interface

Ví dụ:
public abstract class Shape {
public abstract void draw();
}

Sử dụng Abstract class khi chúng ta chỉ có thể hoàn thành một vài chức năng (method/ function) chuẩn của hệ thống, một vài chức năng còn lại các lớp extends phải hoàn thành. Những tính năng đã hoàn thành này vẫn sử dụng như bình thường, đây là những tính năng chung.

**Interface**

Thể hiện tính trừu tượng 100% (Java < 8).

Phiên bản Java < 8, Interface chỉ có thể có phương thức abstract.
Phiên bản Java 8, có thể thêm default và static methods.
Phiên bản Java 9, có thể thêm private methods

Interface hỗ trợ đa kế thừa

Interface chỉ có các biến static final

Interface không thể có phương thức static, main hoặc constructor.

Từ khóa interface được sử dụng để khai báo Interface

Interface không cung cấp trình triển khai cụ thể của lớp abstract

Ví dụ:
public interface Drawable {
void draw();
}

Sử dụng Interface khi bạn muốn tạo dựng một bộ khung chuẩn gồm các chức năng (method/ function) mà tất cả module/ project cần phải có. Các module phải implements tất cả chức năng đã được định nghĩa.

Nói về Abtract Class và Interface, đôi khi bạn sẽ gặp một số cách gọi: Khi một class extend một class/ abtract class thì có nghĩa là ta đang thể hiện mối quan hệ is-a (là), còn khi implement một interface, thì ta đang thể hiện mối quan hệ has-a (có, hay thực hiện)

*VD:*

``` java 
// Programmer là Person, thực hiện việc Programming, Debugging
 
class Programmer extends Person implements Programming, Debugging {}
 
// Rectangle là Shape, có Width, Height
 
class Rectangle extends Shape implements Width, Height {}

```
