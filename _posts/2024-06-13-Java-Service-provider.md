---
layout: post
title: Java Service Provider Interface (SPI) 
categories: SPI
---

### Giới thiệu SPI 

*link: https://gpcoder.com/5554-gioi-thieu-java-service-provider-interface-spi-tao-cac-ung-dung-java-de-mo-rong/*

### 1. Các thành phần của Java Service Provider Interface (SPI)

Một Service Provider framework là một hệ thống trong đó nhiều nhà cung cấp dịch vụ (Service Provider) implement một Service và hệ thống này cung cấp các implement cho client sử dụng. SPI giúp giảm kết dính và che dấu thông tin giữa các thành phần của ứng dụng.

Java SPI định nghĩa các thành phần chính sau:

+ **Service :** là một tập hợp các interface/ abstract class mà nó cung cấp quyền truy cập vào một số chức năng hoặc tính năng ứng dụng cụ thể.

+ **Service Provider Interface (SPI):** là một tập hợp interface/ abstract hoạt động như một proxy hoặc điểm cuối (endpoint) cho service.

+ **Service Provider :** là một triển khai cụ thể của SPI. Nó chứa một hoặc nhiều class cụ thể implements hoặc extends một Service

+ **ServiceLoader :** là thành phần không thể thiếu của SPI. Nó có vai trò tìm kiếm và load các implements khi được yêu cầu. Nó sử dụng context classpath để xác định vị trí các provider implement provider và lưu chúng vào cache để tăng performance.

2. Cài đặt và sử dụng SPI

Giả sử chúng ta có một ứng dụng cung cấp các service cho các ứng dụng khác sử dụng. Mỗi service của chúng ta có nhiều provider (implementation) khác nhau. Các ứng dụng có thể lựa chọn provider mà nó cần hoặc tự implement một provider khác tuân theo đặc tả của SP

![_config.yml]({{ site.baseurl }}/images/spi-01.png)

Để dễ dàng thêm bớt service mà không ảnh hưởng đến các ứng dụng khác, chúng ta sẽ sử dụng tính năng có sẵn trong Java 6 là ServiceLoader. Hãy xem ví dụ sau:

Đầu tiên, tạo project maven với tên là JavaSPI. Cấu hình file pom.xml như sau:

```java

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
 
    <groupId>com.gpcoder</groupId>
    <artifactId>JavaSPI</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>
 
    <name>JavaSPI</name>
    <url>http://maven.apache.org</url>
 
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
 
    <dependencies>
    </dependencies>
</project

```

Tiếp theo tạo một Service, chẳng hạn chúng ta cung cấp Serivce hỗ trợ tìm kiếm. Service này chỉ gồm 1 phương thức đơn giản search().

- **SearchService.java**

```java

package com.gpcoder.spi;
 
import java.util.List;
 
public interface SearchService {
 
    List<Result> search(String text);
}

```

Tiếp theo, chúng ta sẽ tạo các Provider được cung cấp bởi hệ thống:

+ DefaultSearchService : hỗ trợ search thông thường.
+ FullTextSearchService : hỗ trợ search toàn văn.

- **DefaultSearchService.java**

```java
package com.gpcoder.spi;
 
import java.util.List;
 
public class DefaultSearchService implements SearchService {
 
    @Override
    public List<Result> search(String text) {
        System.out.println("DefaultSearchService#search()");
        return null;
    }
}

```

- **FullTextSearchService.java**

```java
package com.gpcoder.spi;
 
import java.util.List;
 
public class FullTextSearchService implements SearchService {
 
    @Override
    public List<Result> search(String text) {
        System.out.println("FullTextSearchService#search()");
        return null;
    }
}

```

Để sử dụng SPI, chúng ta cần tạo một file cấu hình:

+ Tên file: là tên đầy đủ của Service, bao gồm cả tên package. Ví dụ: com.gpcoder.spi.SearchService

+ Nội dung file: là tên đầy đủ của Service implement, mỗi implement trên một dòng. Ví dụ: com.gpcoder.spi.DefaultSearchService  com.gpcoder.spi.FullTextSearchService

+ Vị trí: đặt file này trong thư mục **resources/META-INF/services**

Cuối cùng, chúng ta sẽ sử dụng ServiceLoader để lấy các Provider đã được đăng ký thông qua cấu hình SPI ở trên

```java
package com.gpcoder.spi;
 
import java.util.ServiceLoader;
 
public class ServiceLoaderExample {
    public static void main(String[] args) {
        System.out.println("App started ");
 
        ServiceLoader<SearchService> serviceLoader = ServiceLoader.load(SearchService.class);
        for (SearchService provider : serviceLoader) {
            provider.search("");
        }
 
        System.out.println("App finished ");
    }
}

```

Output của chương trình:

```java

App started 
DefaultSearchService#search()
FullTextSearchService#search()
App finished

```

Đặt trường hợp bây giờ chúng ta có một project khác, cần sử dụng các Service được cung cấp bởi project trên. Tiếp tục, chúng ta tạo một project maven khác tên là **JavaSPIChild1** và cấu hình lại file **pom.xml** như sau:

```java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
 
    <groupId>com.gpcoder.child1</groupId>
    <artifactId>JavaSPIChild1</artifactId>
    <packaging>jar</packaging>
 
    <name>JavaSPIChild1</name>
    <url>http://maven.apache.org</url>
 
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
 
    <parent>
        <groupId>com.gpcoder</groupId>
        <artifactId>JavaSPI</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath>../JavaSPI</relativePath>
    </parent>
 
    <dependencies>
        <dependency>
            <groupId>com.gpcoder</groupId>
            <artifactId>JavaSPI</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>
</project>

```


Tiếp theo chỉnh sửa lại một chút file pom.xml của project JavaSPI, thay đổi packing từ jar thành pom.

```java
<packaging>pom</packaging>
```

Chi tiết về cách tạo và cấu hình project maven với nhiều module các bạn vui lòng xem lại bài viết: Xây dựng dự án nhiều Module với Maven.

Giả sử trong ứng dụng con này chúng ta cần sử dụng một Provider khác. Chúng ta có thể thực hiện bằng cách tạo một class mới implements từ SearchService.

```java

package com.gpcoder.child1;
 
import java.util.List;
 
import com.gpcoder.spi.Result;
import com.gpcoder.spi.SearchService;
 
public class Child1SearchService implements SearchService {
 
    @Override
    public List<Result> search(String text) {
        System.out.println("Child1SearchService#search()");
        return null;
    }
}

```

Để sử dụng SPI, chúng ta cần tạo một file cấu hình com.gpcoder.spi.SearchService trong thư mục resources/META-INF/services như sau:

```java
com.gpcoder.spi.DefaultSearchService
com.gpcoder.child1.Child1SearchService
```

Lưu ý: chỉ khai báo các Service mà project JavaSPIChild1 cần sử dụng. Trong ví dụ này, chúng ta chỉ sử dụng DefaultSearchService và Child1SearchService.

Bây giờ, chúng ta sẽ sử dụng ServiceLoader để lấy các Provider đã được đăng ký thông qua cấu hình SPI ở trên:

```java
package com.gpcoder.child1;
 
import java.util.ServiceLoader;
 
import com.gpcoder.spi.SearchService;
 
public class Child1ServiceLoaderExample {
    public static void main(String[] args) {
        System.out.println("App started ");
         
        ServiceLoader<SearchService> providers = ServiceLoader.load(SearchService.class);
        for (SearchService provider : providers) {
            provider.search("");
        }
 
        System.out.println("Child1SearchService: ");
        SearchService service = getServiceByName(Child1SearchService.class.getCanonicalName());
        service.search("");
         
        System.out.println("App finished ");
    }
 
    public static SearchService getServiceByName(String className) {
        ServiceLoader<SearchService> providers = ServiceLoader.load(SearchService.class);
        for (SearchService provider : providers) {
            if (provider.getClass().getCanonicalName().equals(className)) {
                return provider;
            }
        }
        return null;
    }
}
```

Output của chương trình:

```java
App started
DefaultSearchService#search()
Child1SearchService#search()
Child1SearchService:
Child1SearchService#search()
App finish

```

Như bạn thấy, ứng dụng của chúng ta rất dễ dàng thêm/ bớt module mà không cần phải thay đổi hay chỉnh sửa code ở project ch

Toàn bộ cấu trúc thư mục của project như sau:

![_config.yml]({{ site.baseurl }}/images/spi-02.png)