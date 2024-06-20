---
layout: post
title: Creating a realms
categories: server-administrarion
---

### Creating a realm

link: https://www.keycloak.org/docs/latest/server_admin/#proc-creating-a-realm_server_administration_guide

you create a realm to provide a management space where you can create users and give them perissions to use applications. At first login, you are typically (*tieu bieum thong thuong*) in the master realm, the top-level realm from which you create other realms.

when deciding what realms you need, consider (*coi nhu, xem xet*) the kind of isolation you want to have for your users and applications. For example, you might create a realm for the employees of your company and a separate (*rieng biet*). realm for your customers. Your employees would log into the employee realm and only be able to visit internal company application. Customers would login the customer realm and pnly be able to interact (*tuong tac*) with customer-facing apps (*cac ung dung huong toi khach hang*).

![_config.yml]({{ site.baseurl }}/images/cr-01.png)

![_config.yml]({{ site.baseurl }}/images/cr-02.png)

--------------------------------------------------------------------------------------

### Configuring SSL for a realm

Each realm has an associated (*co lien quan*) SSL Mode, which defines the SSL/HTTPS requirements for interacting with the realm.
Browsers and applications that interact with the realm honnor (*ton trong*) the SSL/HHTPS requirements defined by the SSL Mode or they cannot interact with the server.

![_config.yml]({{ site.baseurl }}/images/cr-03.png)

3. Set **Require SSL** to one of the following SSL modes:

+ **External requests** Users can interact with Keycloak without SSL so long as they stick to private IP addresses susch as localhost, 127.0.0.1, 10.x.x.x, 192.168.x.x, and 172.16.x.x. If you to access Keycloak without SSL from a non-private IP addressm you will get an error.

+ **None** Keycloak does not require SSL. This choice applies only in development when you are experimenting and do not plan to support this deployment.

+ **All request**  Keycloak requires SSL for all IP addresses.

---------------------------------------------------------------------------------------

### Configuring email for a realm

Keycloak sends emails to users to verify their email addresses,  when they forget their passwords, or when an administrator needs to receive notifications about a server event. To enable Keycloak to send emails, you provide Keycloak with your SMTP server settings.

Procedure
1. Click Realm settings in the menu.

2. Click the Email tab.

![_config.yml]({{ site.baseurl }}/images/cr-04.png)

3. Fill in the fields and toggle the switches as needed.

**Template**

**From**

    + **From** denotes (*bieu thi*) the address used for the **From** SMTP-Header for the email sent.

**From display name**

    + From display name allows (*cho phep*) to configure a user-friendly email address aliases (*bi danh*) (optional). If not set the plain From email address will be displayed in email clients.

**Reply to**

    + Reply to denotes the address