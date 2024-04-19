---
layout: post
title: Migration data với Flyway sử dụng Maven
categories: Java-8
---

### Migration data với Flyway

+ Flyway là gì ?

+ Flyway hoạt động như thế nào ?

+ Các loại migrations

+ Quy tắc đặt tên version

+ Sample - Tích hợp flyway maven plugin

+ Cấu hình maven profile với flyway

+ Maven profile là gì ?

+ Config maven profile

+ Migrating với các servers

### Flyway là gì ?

Flyway là một java open source library hỗ trợ migrate data cho các ứng dụng, một khi chúng ta cần:

+ Nâng cấp database do thay đổi cấu trúc hoặc dữ liệu.

+ Quản lý lịch sử thay đổi version một cách chặt chẽ.

+ Thực thi migrating một cách tự động.

+ Đồng bộ data structure giữa các server development.

### Flyway hoạt động như thế nào ?

Flyway tạo ra một table có tên **SCHEMA_VERSION** dùng để quản lý lịch sử phiên bản cũng như trạng thái của database.

Các file chạy migrate có thể là file **Sql script** hoặc **Java API** là những class VerXXX_Jdbc*** được extends từ flyway core. Các file này đều được đặt tên theo một quy tắc nhất định để xác định version number.

Tại mỗi thời điểm chạy migration. flyway sẽ kiểm tra SCHEMA_VERSION và quét cần migrate để kiểm tra việc migrating tới một version mới hay không. Nếu version đó nhỏ hơn hoặc bằng version hiện tại quá trình này sẽ được bỏ qua.

![_config.yml]({{ site.baseurl }}/images/flyway-1.png)

### Các loại migrations

Flyway support **2** loại migrations là **Versioned migrations** và **Repeatable migrations**.

- **Versioned migrations:** là các version được định danh và có đánh số duy nhất với các version khác, và thường được sử dụng:

+ Creating/altering/dropping/indexes/foreign keys/enums/UDTs/ ...

+ Reference data updates

+ User data corrections

- **Repeatable migraions:** Repeatable thì lại không có version, thay vì đó chúng luôn thực thi sau **versioned migrations** mỗi khi có checksum thay đổi từ table **SCHEMA_VERSION**. Thường được sử dụng:

+ (Re-) creating views/procedures/functions/packages/ ...

+ Bulk reference data reinserts.


### Quy tắc đặt tên version

Flyway có thể migrate data bằng file Sql hoặc từ Java class, vì vậy quy tắc đặt tên biến có những sự khác nhau như sau :

![_config.yml]({{ site.baseurl }}/images/flyway-2.png)

Tên file sẽ theo format :

+ **prefix:** Có thể sử dụng prefix khác, default: V cho versioned migrations, R cho repeatable migrations.

+ **version:** (chỉ áp dung cho Versioned migrations) có thể thêm dấu . hoặc _ để thêm minor version, ví dụ như : V1_2

+ **separator:** Có thể sử dụng separator khác, default: __ (2 dấu gạch dưới __)

+ **description:** Dấu gạch dưới hoặc khoảng trắng để phân biệt các từ.

+ **suffix:** Có thể thiết lập suffix khác, default: .sql

![_config.yml]({{ site.baseurl }}/images/flyway-3.png)

Với Java Migrations thì duy nhất có 2 điểm khác biệt.

+ **prefix:** luôn luôn là V cho versioned migrations và R cho repeatable migrations.

+ **description:** luôn luôn là dấu gạch dưới (_) để phân biệt các từ.

### Sample - Tích hợp flyway maven plugin

#### Tạo maven project

#### Add Jdbc Driver Dependency

**Thêm dependency trong file pom.xml**

```java
    <dependencies>
        <!--Add postgres jdbc driver-->
        <dependency>
            <groupId>postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>9.1-901.jdbc4</version>
        </dependency>
    </dependencies>
```

#### Cấu hinh flyway maven plugin

Thêm nội dung sau vào file pom.xml , phần này sẽ thiết lập các parameters cho plugin flyway-maven-plugin cái sẽ sử dụng cho target build của maven.

```java
<project>
....
    <build>
        <plugins>
            <plugin>
                <groupId>org.flywaydb</groupId>
                <artifactId>flyway-maven-plugin</artifactId>
                <version>4.2.0</version>
                <configuration>
                    <url>jdbc:postgresql://localhost:5432/flyway_demo?currentSchema=public</url>
                    <user>postgres</user>
                    <password>postgres</password>
                </configuration>
            </plugin>
        </plugins>
    </build>
...
</project>

```

+ **url :** connection string đến postgres database.

+ **user :** user name connect đến database.

+ **password :** password connect đến database.

Ngoài ra, bạn có thể cấu hình một vài thông tin khác như :

+ **locations :** chỉ định folder migration , default : resources/db/migration ( đã tạo ở trên).

+ **sqlMigrationPrefix :** prefix trong tên file, default là V ( xem lại Quy tắc đặt tên version)

+ **sqlMigrationSuffix :** suffix trong tên file, default là .sql ( xem lại Quy tắc đặt tên version)

#### Migrating

Tạo file **V1__Create_employee_table.sql** trong thư mục **resources/db/migration** :

```java
CREATE TABLE employee (
    employee_id INT NOT NULL,
    employee_name VARCHAR (100) NOT NULL,
    employee_address VARCHAR (256) NOT NULL
);

```

Cấu trúc hiện tại như sau :

![_config.yml]({{ site.baseurl }}/images/flyway-4.png)

Để chạy migration , thực hiện command sau trong root folder của project :

```java
mvn flyway:migrate
```

Output :

```java
[INFO] Successfully validated 1 migration (execution time 00:00.005s)
[INFO] Creating Metadata table: "public"."schema_version"
[INFO] Current version of schema "public": << Empty Schema >>
[INFO] Migrating schema "public" to version 1 - Create employee table
[INFO] Successfully applied 1 migration to schema "public" (execution time 00:00.038s).

```

Tương tự, giờ chúng ta sẽ thử insert một vài records vào **employee table**.

Tạo file **V2__Add_employee_table.sql** trong thư mục **resources/db/migration** :

```java
INSERT INTO employee (employee_id, employee_name, employee_address) VALUES
  (1, 'Nguyen Van A', 'Ha Noi'),
  (2, 'Tran Van B', 'Bac Giang'),
  (3, 'Nguyen Thi C', 'Hai Duong');

```

Và chạy maven flyway một lần nữa.

```java
mvn flyway:migrate

```

Nào, giờ thì version up sẽ migrating là 2 như log bên dưới.

```java
[INFO] Database: jdbc:postgresql://localhost:5432/flyway_demo?currentSchema=public (PostgreSQL 9.5)
[INFO] Successfully validated 2 migrations (execution time 00:00.009s)
[INFO] Current version of schema "public": 1
[INFO] Migrating schema "public" to version 2 - Add employee table
[INFO] Successfully applied 1 migration to schema "public" (execution time 00:00.017s).


```

### Cấu hình maven profile với flyway


#### Maven profile là gì ?

Maven profile cho phép chúng ta switch các file config cho từng môi trường khác nhau, rất thích hợp để tùy biến cho các môi trường khác nhau như test, staging hoặc production.

Chỉ đơn giản với command :

```java
mvn -P <profile-name>

```

Các profile được khai báo trong file pom.xml và luôn có một profile được **active (activeProfiles)**, ở thời điểm build nó sẽ chọn lựa ra **profile-name** tương ứng để lấy các parameter settings cho các môi trường khác nhau.
 Ví dụ : các path folder cấu hình trên các servers, thông tin kết nối database..vv

 #### Config maven profile với flyway.

 Giả sử mình đang có 3 server **local, test và staging** lần lượt với các ip tương ứng là :

+ **local :** localhost - máy local dev

+ **test :** 192.168.5.173 - máy cho testing trong giai đoạn phát triển

+ **staging :** 192.168.5.195 máy cho testing gian đoạn pre-release

Những việc cần làm :

Những việc cần làm :

+ Tạo 3 file flyway.properties cho 3 môi trường tương ứng.

+ Tạo 3 profile cho 3 môi trường tương ứng trong pom.xml

![_config.yml]({{ site.baseurl }}/images/flyway-5.png)

**src/resources/config/local/flyway.properties:**

```java
flyway.url=jdbc:postgresql://localhost:5432/flyway_demo?currentSchema=public
flyway.user=postgres
flyway.password=postgres

```

**src/resources/config/test/flyway.properties:**

```java
flyway.url=jdbc:postgresql://192.168.5.173:5432/flyway_demo?currentSchema=public
flyway.user=postgres
flyway.password=postgres

```

**src/resources/config/staging/flyway.properties:**

```java
flyway.url=jdbc:postgresql://192.168.5.195:5432/flyway_demo?currentSchema=public
flyway.user=postgres
flyway.password=postgres

```

#### Tạo maven profiles

Quay lại với pom.xml, **tạm thời comment out thẻ <build> - những gì đã config ở trên**

Lần lượt tạo các profile theo format sau, trong đó:

+ id : tên profile lần lượt là local, test và staging.

+ configFile : path tới file flyway.properties tương ứng cho từng môi trường

```java

<project>
...
    <profiles>
        <profile>
            <id>local</id>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.flywaydb</groupId>
                        <artifactId>flyway-maven-plugin</artifactId>
                        <version>4.2.0</version>
                        <configuration>
                            <configFile>src/main/resources/config/local/flyway.properties</configFile>
                        </configuration>
                    </plugin>
                </plugins>
            </build>
        </profile>

    <!--Other profiles here-->

    </profiles>
....
<project>

```

#### Migrating với các servers

Sau khi cấu hình 3 profiles cho các môi trường, để migation ta dùng command sau: 

```java
mvn flyway:migrate -P <profile_name>
```

#### Migrating trên local server

Giờ minh sẽ thử chạy lại với local server theo command sau :

```java
mvn flyway:migrate -P local

```

Output :

```java
[INFO] Database: jdbc:postgresql://localhost:5432/flyway_demo?currentSchema=public (PostgreSQL 9.5)
[INFO] Successfully validated 2 migrations (execution time 00:00.008s)
[INFO] Current version of schema "public": 2
[INFO] Schema "public" is up to date. No migration necessary.

```

Dễ nhận thấy rằng, database không có gì thay đổi ở phía local, vì nó đã được chạy 2 lần trước đó khi chưa tích hợp maven profile.

Flyway sẽ kiểm tra version hiện tại với table SCHEMA_VERSION và nhận thấy không có gì thay đổi và bỏ qua lần migrating này.

#### Migrating trên test server

Tương tự như local, phía server test và staging cũng thực hiện với command sau :

```java
mvn flyway:migrate -P test

```

Output :

```java
[INFO] Database: jdbc:postgresql://192.168.5.173:5432/flyway_demo?currentSchema=public (PostgreSQL 9.5)
[INFO] Creating schema "jibunshoten" ...
[INFO] Creating Metadata table: "jibunshoten"."schema_version"
[INFO] Current version of schema "jibunshoten": null
[INFO] Migrating schema "jibunshoten" to version 1 - Create employee table
[INFO] Migrating schema "jibunshoten" to version 2 - Add employee table
[INFO] Successfully applied 2 migrations to schema "jibunshoten" (execution time 00:00.053s).

```
Nhìn vào log bạn cũng sẽ thấy, migrating được thực hiện 2 lần, lần lượt với version 1 và version 2. Và đó là kết quả cho lần đầu migrating trên server mới.

