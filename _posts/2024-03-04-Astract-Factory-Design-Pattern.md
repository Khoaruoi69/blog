---
layout: post
title: Abstract Factory - Design Pattern
categories: Design-pattern creational-design-pattern
---

#### 1. Abstract Factory Pattern là gì ?

**Factory Method Design Pattern** hay gọi ngắn gọn là **Factory Pattern** là một trong những Pattern thuộc nhóm **Creational Design Pattern***.
 Nhiệm vụ của **Factory Pattern** là quản lý và trả về các đối tượng theo yêu cầu, giúp cho việc khởi tạo đổi tượng một cách linh hoạt hơn.

Trong **Abstract Factory pattern**, một interface có nhiệm vụ tạo ra một Factory của các object có liên quan tới nhau mà không cần phải chỉ ra trực tiếp các class của object. Mỗi Factory được tạo ra có thể tạo ra các object bằng phương pháp giống như Factory pattern.

Hãy tưởng tượng, Abstract factory như là một nhà máy lớn chứa nhiều nhà máy nhỏ, trong các nhà máy đó có những xưởng sản xuất, các xưởng đó tạo ra những sản phẩm khác nhau.

### 2. Triển khai Abstract Factory Pattern

Một Abstract Factory Pattern bao gồm các thành phần cơ bản sau:

+ **Abstract-Factory:** khai báo dạng interface hoặc abstract class chứa các phương thức để tạo ra các đối tượng abstract.

+ **Concret-Factory:** Xây dựng, cài đặt các phương thức tạo các đối tượng cụ thể.

+ **Product:** Cài đặt các đối tượng cụ thể, cài đặt các phương thức được quy định tại Abstract Product.

*Ví dụ:* Một công ty đồ nội thất chuyên sản xuất ghế (Chair): ghế nhựa (PlasticChair) và ghế gỗ (WoodChair). 
Với tình hình kinh doanh ngày càng thuận thợi nên công ty quyết định mở rộng thêm sản xuất bàn (Table). 
Với lợi thế là đã có kinh nghiệm từ sản xuất ghế nên công ty vẫn giữ chất liệu là nhựa (PlasticTable) và gỗ (WoodTable) cho sản xuất bàn. 
Tuy nhiên, quy trình sản xuất ghế/ bàn theo từng chất liệu (MaterialType) là khác nhau. 
Nên công ty tách ra là nhà máy (Factory): 1 cho sản xuất vật liệu bằng nhựa (PlasticFactory), 1 cho sản xuất vật liệu bằng gỗ (WoodFactory), nhưng cả 2 đều có thể sản xuất ghế và bàn (FunitureAbstractFactory).
Khi khách hàng cần mua một món đồ nào, khách hàng (Client) chỉ cần đến cửa hàng để mua (FunitureFactory). Khi đó ứng với từng hàng hóa và vật liệu sẽ được chuyển về phân xưởng tương ứng để sản xuất (createXXX) ra bàn (Table) và ghế (Chair).

Hệ thống được minh họa sau:

![_config.yml]({{ site.baseurl }}/images/system-abstract-factory.png)

**Chương trình được cài đặt theo Abstract Factory Pattern như sau:**

**--------Super Factory Class:--------**

```java
package com.gpcoder.patterns.creational.abstractfactory.factory;
 
import com.gpcoder.patterns.creational.abstractfactory.MaterialType;
import com.gpcoder.patterns.creational.abstractfactory.factory.impl.FlasticFactory;
import com.gpcoder.patterns.creational.abstractfactory.factory.impl.WoodFactory;
 
public class FurnitureFactory {
 
    private FurnitureFactory() {
 
    }
 
    // Returns a concrete factory object that is an instance of the
    // concrete factory class appropriate for the given architecture.
    public static FurnitureAbstractFactory getFactory(MaterialType materialType) {
        switch (materialType) {
        case FLASTIC:
            return new FlasticFactory();
        case WOOD:
            return new WoodFactory();
        default:
            throw new UnsupportedOperationException("This furniture is unsupported ");
        }
    }
}
```

**------AbstractFactory:------**

```java
package com.gpcoder.patterns.creational.abstractfactory.factory;
 
import com.gpcoder.patterns.creational.abstractfactory.chair.Chair;
import com.gpcoder.patterns.creational.abstractfactory.table.Table;
 
public abstract class FurnitureAbstractFactory {
 
    public abstract Chair createChair();
 
    public abstract Table createTable();
     
}
```

**--------ConcreteFactory:------**

**FlasticFactory:**

```java
package com.gpcoder.patterns.creational.abstractfactory.factory.impl;
 
import com.gpcoder.patterns.creational.abstractfactory.chair.Chair;
import com.gpcoder.patterns.creational.abstractfactory.chair.PlasticChair;
import com.gpcoder.patterns.creational.abstractfactory.factory.FurnitureAbstractFactory;
import com.gpcoder.patterns.creational.abstractfactory.table.PlasticTable;
import com.gpcoder.patterns.creational.abstractfactory.table.Table;
 
public class FlasticFactory extends FurnitureAbstractFactory {
 
    @Override
    public Chair createChair() {
        return new PlasticChair();
    }
 
    @Override
    public Table createTable() {
        return new PlasticTable();
    }
 
}

```

**WoodFactory:**

```java
package com.gpcoder.patterns.creational.abstractfactory.factory.impl;
 
import com.gpcoder.patterns.creational.abstractfactory.chair.Chair;
import com.gpcoder.patterns.creational.abstractfactory.chair.WoodChair;
import com.gpcoder.patterns.creational.abstractfactory.factory.FurnitureAbstractFactory;
import com.gpcoder.patterns.creational.abstractfactory.table.Table;
import com.gpcoder.patterns.creational.abstractfactory.table.WoodTable;
 
public class WoodFactory extends FurnitureAbstractFactory {
 
    @Override
    public Chair createChair() {
        return new WoodChair();
    }
 
    @Override
    public Table createTable() {
        return new WoodTable();
    }
}
```

**---------AbstractProduct và Product:----------**

**Chair:**

*interface Chair*

```java
package com.gpcoder.patterns.creational.abstractfactory.chair;
 
public interface Chair {
    void create();
}
```

*class implement Chair*

```java
package com.gpcoder.patterns.creational.abstractfactory.chair;
 
public class PlasticChair implements Chair {
    @Override
    public void create() {
        System.out.println("Create plastic chair");
    }
}
```

```java
package com.gpcoder.patterns.creational.abstractfactory.chair;
 
public class WoodChair implements Chair {
    @Override
    public void create() {
        System.out.println("Create wood chair");
    }
}
```


**Table:**

*interface class*

```java
package com.gpcoder.patterns.creational.abstractfactory.table;
 
public interface Table {
    void create();
}
```

*implement class Table*

```java
package com.gpcoder.patterns.creational.abstractfactory.table;
 
public class PlasticTable implements Table {
    @Override
    public void create() {
        System.out.println("Create plastic table");
    }
}
```

```java
package com.gpcoder.patterns.creational.abstractfactory.table;
 
public class WoodTable implements Table {
    @Override
    public void create() {
        System.out.println("Create wood table");
    }
}
```

**---------Material type----------**

```java
public enum MaterialType {
    FLASTIC, WOOD
}
```

**-----------------Client----------------**

```java
package com.gpcoder.patterns.creational.abstractfactory;
 
import com.gpcoder.patterns.creational.abstractfactory.chair.Chair;
import com.gpcoder.patterns.creational.abstractfactory.factory.FurnitureAbstractFactory;
import com.gpcoder.patterns.creational.abstractfactory.factory.FurnitureFactory;
import com.gpcoder.patterns.creational.abstractfactory.table.Table;
 
public class Client {
 
    public static void main(String[] args) {
 
        FurnitureAbstractFactory factory = FurnitureFactory.getFactory(MaterialType.FLASTIC);
 
        Chair chair = factory.createChair();
        chair.create(); // Create plastic chair
 
        Table table = factory.createTable();
        table.create(); // Create plastic table
    }
}
```

Như bạn thấy, phía **Client** chỉ cần gọi phương thức **FurnitureFactory.getFactory()** là có thể sử dụng được các dịch vụ của một Factory bất kỳ.

Khi hệ thống phát triển cần mở rộng thêm 1 nhà máy khác, chẳng hạn sản xuất hàng hóa bằng inox, thì đơn giản cần tạo thêm một class mới implement từ **FurnitureAbstractFactory**, và thêm vào logic khởi tạo **Funiture trong FurnitureFactory**. Nó không làm ảnh hưởng đến code ở phía Client.

### 3. Lợi ích của Astract Factory Pattern là gì ?

+ Các lợi ích của **Factory Pattern** cũng tương tự như **Factory Method Pattern** như: cung cấp hướng tiếp cận với Interface thay thì các implement, che giấu sự phức tạp của việc khởi tạo các đối tượng với người dùng (client), độc lập giữa việc khởi tạo đối tượng và hệ thống sử dụng, …

+ Giúp tránh được việc sử dụng điều kiện logic bên trong Factory Pattern. Khi một **Factory Method** lớn (có quá nhiều sử lý if-else hay switch-case), chúng ta nên sử dụng theo mô hình **Abstract Factory** để dễ quản lý hơn (cách phân chia có thể là gom nhóm các sub-class cùng loại vào một Factory).

+ **Abstract Factory Pattern** là factory của các factory, có thể dễ dạng mở rộng để chứa thêm các factory và các sub-class khác.
Dễ dàng xây dựng một hệ thống đóng gói (encapsulate): sử dụng được với nhiều nhóm đối tượng (factory) và tạo nhiều product khác nhau.