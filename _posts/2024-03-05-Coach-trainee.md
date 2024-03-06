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

**Tạo Controller**: dùng để call đến server getbody()

``` java
package com.test.client.controller;

import com.netflix.appinfo.InstanceInfo;
import com.netflix.discovery.EurekaClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.http.HttpMethod;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class ClientController {
    @Autowired
    private EurekaClient client;
    @Autowired
    private RestTemplateBuilder templateBuilder;

    @RequestMapping("/")
    public String callService(){
        InstanceInfo instanceInfo = client.getNextServerFromEureka("service", false);
        String url = instanceInfo.getHomePageUrl();

        RestTemplate restTemplate = templateBuilder.build();
        ResponseEntity<String> response = restTemplate.exchange(url, HttpMethod.GET,null,String.class);
        return response.getBody();
    }
}
```

### Spring microservices phần 4 - Tạo config server để đọc thông tin cấu hình từ github

![_config.yml]({{ site.baseurl }}/images/config-server-github.png)

**Tạo git repo**
![_config.yml]({{ site.baseurl }}/images/git-repo.png)

*Link Git repo:* 

*[spring-cloud-sample-config-repo](https://github.com/Khoaruoi69/spring-cloud-sample-config-repo)*

**Tạo config server**

![_config.yml]({{ site.baseurl }}/images/config-server.png)

```java
package com.test.configserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.config.server.EnableConfigServer;

@SpringBootApplication
@EnableDiscoveryClient
@EnableConfigServer
public class ConfigserverApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConfigserverApplication.class, args);
	}

}

```

**application properties**

```java

spring.application.name=config-service
server.port=8888
eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka
spring.cloud.config.server.git.uri =https://github.com/Khoaruoi69/spring-cloud-sample-config-repo.git
spring.cloud.config.server.git.default-label=main

```

**run port 8888** /abc/default -- /config-server/default --- config-server/prod



### Spring microservices phần 5 - Tạo config client và đọc thông tin cấu hình thông qua config server

![_config.yml]({{ site.baseurl }}/images/config-client.png)

**Tạo ClientConfig**

```java
package com.test.configclient.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "sample")
public class ClientConfig {
    private String properties1;

    public String getProperties1() {
        return properties1;
    }

    public void setProperties1(String properties1) {
        this.properties1 = properties1;
    }
}

```

 **Tạo ConfigClientController**

 ```java
 package com.test.configclient.controller;

import com.test.configclient.config.ClientConfig;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ConfigClientController {
    @Autowired
    private ClientConfig clientConfig;

    @Value("${sample.properties2}")
    private String property2;
    @RequestMapping("/config")
    public String printConfig() {
        return clientConfig.getProperties1() + "------" + property2;
    }
}

 ```

 **Bootstrap.properties**

 ```java
 
spring.application.name=config-client
spring.cloud.config.discovery.enabled=true
eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka
spring.profiles.active=prod
server.port=7007
 ```

### Spring microservices phần 6 - Hướng dẫn cách refresh config client để cập nhật thông tin cấu hình


**Change information config: prod.properties**

```java
sample.properties1=property 1 for config client with prob profile change
sample.properties2=property 2 for config client with prob profile change
```

**ClientConfig**

```java
package com.test.configclient.controller;

import com.test.configclient.config.ClientConfig;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.context.annotation.RequestScope;

@RestController
@RequestScope
public class ConfigClientController {
    @Autowired
    private ClientConfig clientConfig;

    @Value("${sample.properties2}")
    private String property2;
    @RequestMapping("/config")
    public String printConfig() {
        return clientConfig.getProperties1() + "------" + property2;
    }
}

```

**boostrap.properties**

```java

spring.application.name=config-client
spring.cloud.config.discovery.enabled=true
eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka
spring.profiles.active=prod
server.port=7007
# refresh update information config
management.endpoints.web.exposure.include=refresh   
# call post man : POST localhost:8080/actuator/refresh
```