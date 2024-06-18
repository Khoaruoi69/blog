---
layout: post
title: Authentication SPI
categories: SPI
---

### Authentication SPI

KeyCloak includes a range (*phạm vi*) of different authentication mechanisms (*cơ chế*): kerberos, password, otp, and others. 
These mechanisms may not meet all (*không đáp ứng được tất cả*) of your requirements and you may want to plug in your own custom ones.
Keycloak provides an authentication SPI that you can use to write new plugins.
The Admin Console supports applying, ordering, and configuring these new mechanism.
