---
layout: post
title: Getting Started gRPC
categories: gRPC
---

## Project Setup

- link: *https://yidongnan.github.io/grpc-spring-boot-starter/en/server/getting-started.html#project-setup*

- Trước khi bắt đầu thêm các phụ thuộc, hãy bắt đầu với một số gợi ý của chúng tôi cho việc thiết lập dự án của bạn.

![_config.yml]({{ site.baseurl }}/images/pro-setup-01.png)

- Chúng tôi khuyến nghị chia dự án của bạn thành 2-3 module riêng biệt.

+ 1. **The interface project (Dự án giao diện:)** Chứa các file protobuf thô và tạo ra các lớp mô hình và dịch vụ Java. Bạn có thể chia sẻ phần này.
+ 2. **The server project  (Dự án máy chủ:)** Chứa phần triển khai thực tế của dự án và sử dụng dự án giao diện như một phụ thuộc.
+ 3. **The client projects  (Dự án khách hàng (tùy chọn và có thể có nhiều):)** Bất kỳ dự án khách hàng nào sử dụng các stub đã tạo sẵn để truy cập vào máy chủ.

# Dependencies

## Interface-Project

### Maven (Interface)

```java
    <properties>
        <protobuf.version>3.23.4</protobuf.version>
        <protobuf-plugin.version>0.6.1</protobuf-plugin.version>
        <grpc.version>1.58.0</grpc.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>io.grpc</groupId>
            <artifactId>grpc-stub</artifactId>
            <version>${grpc.version}</version>
        </dependency>
        <dependency>
            <groupId>io.grpc</groupId>
            <artifactId>grpc-protobuf</artifactId>
            <version>${grpc.version}</version>
        </dependency>
        <dependency>
            <!-- Java 9+ compatibility - Do NOT update to 2.0.0 -->
            <groupId>jakarta.annotation</groupId>
            <artifactId>jakarta.annotation-api</artifactId>
            <version>1.3.5</version>
            <optional>true</optional>
        </dependency>
    </dependencies>

    <build>
        <extensions>
            <extension>
                <groupId>kr.motd.maven</groupId>
                <artifactId>os-maven-plugin</artifactId>
                <version>1.7.0</version>
            </extension>
        </extensions>

        <plugins>
            <plugin>
                <groupId>org.xolstice.maven.plugins</groupId>
                <artifactId>protobuf-maven-plugin</artifactId>
                <version>${protobuf-plugin.version}</version>
                <configuration>
                    <protocArtifact>com.google.protobuf:protoc:${protobuf.version}:exe:${os.detected.classifier}</protocArtifact>
                    <pluginId>grpc-java</pluginId>
                    <pluginArtifact>io.grpc:protoc-gen-grpc-java:${grpc.version}:exe:${os.detected.classifier}</pluginArtifact>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>compile</goal>
                            <goal>compile-custom</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```

### Gradle (Interface)

```java
buildscript {
    ext {
        protobufVersion = '3.23.4'
        protobufPluginVersion = '0.8.18'
        grpcVersion = '1.58.0'
    }
}

plugins {
    id 'java-library'
    id 'com.google.protobuf' version "${protobufPluginVersion}"
}

repositories {
    mavenCentral()
}

dependencies {
    implementation "io.grpc:grpc-protobuf:${grpcVersion}"
    implementation "io.grpc:grpc-stub:${grpcVersion}"
    compileOnly 'jakarta.annotation:jakarta.annotation-api:1.3.5' // Java 9+ compatibility - Do NOT update to 2.0.0
}

protobuf {
    protoc {
        artifact = "com.google.protobuf:protoc:${protobufVersion}"
    }
    generatedFilesBaseDir = "$projectDir/src/generated"
    clean {
        delete generatedFilesBaseDir
    }
    plugins {
        grpc {
            artifact = "io.grpc:protoc-gen-grpc-java:${grpcVersion}"
        }
    }
    generateProtoTasks {
        all()*.plugins {
            grpc {}
        }
    }
}

// Optional
eclipse {
    classpath {
        file.beforeMerged { cp ->
            def generatedGrpcFolder = new org.gradle.plugins.ide.eclipse.model.SourceFolder('src/generated/main/grpc', null);
            generatedGrpcFolder.entryAttributes['ignore_optional_problems'] = 'true';
            cp.entries.add( generatedGrpcFolder );
            def generatedJavaFolder = new org.gradle.plugins.ide.eclipse.model.SourceFolder('src/generated/main/java', null);
            generatedJavaFolder.entryAttributes['ignore_optional_problems'] = 'true';
            cp.entries.add( generatedJavaFolder );
        }
    }
}

// Optional
idea {
    module {
        sourceDirs += file("src/generated/main/java")
        sourceDirs += file("src/generated/main/grpc")
        generatedSourceDirs += file("src/generated/main/java")
        generatedSourceDirs += file("src/generated/main/grpc")
    }
}
```

## Server-Project

### Maven (Server)

```java

 <dependencies>
        <dependency>
            <groupId>net.devh</groupId>
            <artifactId>grpc-server-spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>example</groupId>
            <artifactId>my-grpc-interface</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

```

### Gradle (Server)

```java
apply plugin: 'org.springframework.boot'

dependencies {
    compile('org.springframework.boot:spring-boot-starter')
    compile('net.devh:grpc-server-spring-boot-starter')
    compile('my-example:my-grpc-interface')
}

buildscript {
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
    }
}
```

## Client project

*[See the Getting setup Client-project](https://khoaruoi69.github.io/blog/getting-setup-client/)*

# Creating the gRPC-Service Definitions

- Đặt các định nghĩa protobuf / file .proto của bạn trong thư mục src/main/proto. Để viết các file protobuf, vui lòng tham khảo tài liệu chính thức của protobuf.

- Các file .proto của bạn sẽ trông tương tự như ví dụ dưới đây:

```java
syntax = "proto3";

package net.devh.boot.grpc.example;

option java_multiple_files = true;
option java_package = "net.devh.boot.grpc.examples.lib";
option java_outer_classname = "HelloWorldProto";

// The greeting service definition.
service MyService {
    // Sends a greeting
    rpc SayHello (HelloRequest) returns (HelloReply) {
    }
}

// The request message containing the user's name.
message HelloRequest {
    string name = 1;
}

// The response message containing the greetings
message HelloReply {
    string message = 1;
}

```

- Các plugin protobuf đã được cấu hình trong Maven/Gradle sau đó sẽ sử dụng trình biên dịch protoc với plugin protoc-gen-grpc-java và tạo ra các lớp dữ liệu, lớp triển khai dịch vụ gRPC (ImplBase) và các lớp Stub. Xin lưu ý rằng các plugin khác như reactive-grpc có thể tạo ra các lớp bổ sung/thay thế mà bạn phải sử dụng thay thế. Tuy nhiên, chúng có thể được sử dụng theo cách tương tự.
