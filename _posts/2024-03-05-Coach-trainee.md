---
layout: post
title: City Life
categories: Coach-trainee
---

#### Spring microservices phần 1 - Tổng quan

**Tạo discovery-service**

![_config.yml]({{ site.baseurl }}/images/microserive-discovery.png)

Quá trình Client gọi đến Service được gọi là: Service Discovery.

start instance service -> đăng kí instance với Discovery --> Discovery Lưu dữ các instance serivce 

client gọi ---> Discovery trả về instance cho Client

Dựa vào instance này client gọi ---> Service : trả về dữ liệu cho ---> Client

#### B1. Tạo Service Discovery

![_config.yml]({{ site.baseurl }}/images/discoveryServer.png)


**main discoveryApplication**

```java
package com.test.discoveryserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class DiscoveryServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(DiscoveryServerApplication.class, args);
	}

}

```

**application.properties**

``` java
eureka.instance.hostname=discovery-service
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
server.port=8761
```

### Spring microservices phần 2 - Tạo Service và đăng kí Discovery

**Tạo service**

![_config.yml]({{ site.baseurl }}/images/service.png)

**ServerApplication**
``` java
package com.test.server;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@EnableDiscoveryClient
@SpringBootApplication
public class ServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServerApplication.class, args);
	}

}

```

**application.properties**

```java
spring.application.name=service
eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka
service.instance.name = service 01
server.port=9001
```

**Tạo Controller**

``` java
package com.test.server.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ServiceController {

    @Value("${service.instance.name}")
    private String instance;
    @RequestMapping("/")
    public String sayHello(){
        return "Helllo from" + instance;
    }
}

```

**Service đăng kí thành công Discovery**

![_config.yml]({{ site.baseurl }}/images/service-resigter-discovery.png)


### Spring microservices phần 3 - Tạo client, tìm và gọi service từ client thông qua discovery server


**tạo Client service**

![_config.yml]({{ site.baseurl }}/images/client.png)