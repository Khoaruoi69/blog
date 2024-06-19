---
layout: post
title: Implement custom SPI in KeyCloak
categories: SPI
---

### You need the following steps (old version):

+ Be aware (*nhận biết, nhận thức*) of what SPI you want to customize. There are several (*mot so*) options like a SPI for **User Storage, Theming, Authentication etc.** see
*https://www.keycloak.org/docs/latest/server_development/#_providers* for more details.

+ For every SPI you need to build a Java Project. Here is an example for a custom UserStorage SPI via HTTP Client *https://github.com/adviqo-GmbH/keycloak/compare/master...adviqo-GmbH:feature/http-storage* - this is a diff. You need a pom.xml, some java-classes and at least a property under **META-INF/services/org.keycloak.storage.UserStorageProviderFactory** that contains the reference (*tham quyen, giai quyet*) to specific (*cu the*) class 

+ then you need to build the SPI module with maven/java.

+ the result ( here in form of a JAR, but WAR and EAR is possible (*kha thi*) too) must be placed in **<keycloak-home>/standalone/deployments.**

+ KeyCloak will identify (*nhan dang*) the new custom SPI during startup.

That it. Of course - it's not easy and the documentation (*https://www.keycloak.org/docs/latest/server_development/#_providers *) not very helpful for beginners


