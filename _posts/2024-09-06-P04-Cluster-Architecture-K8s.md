---
layout: post
title: Cluster Architecture - Kubernetes 
categories: Kubernetes
---

### Cluster Architecture


- Một cluster gòm  thành phấn chính:

+ Control plane / Master node
+ Worker Node (Node) --> các worker node gọi chung là Data Plane

- Control plane:
    + API-server: nhận 1 request --> Làm việc với các **Worker node**
    + Scheduler: send dữ liệu xuống ETCD (ETCD tính toán xử lý triển khai sau đó send lại cho Api-server) --> chỉ định pod sẽ đc triển khai lên **Woker-node** nào
    + ETCD: dữ liệu dạng khóa ( key - value ). Data store lưu trữ dữ liệu các ứng dụng đang chạy trạng thái ứng dụng ... dữ liệu này là cơ sở để xử lý triển khai sao cho phù hợp.

    + Control manager: quản lý các worker nào đang sống ( baseline ) -> giám sát để cluster ổn định ( làm viejc với Api-server)
    + Clound-manager: 
    + Container-runtime:

- Worker Node:
    + kubelet: nhận 1 yêu cầu từ Api-server sẽ deploy các container mong muốn.
    + kube-proxy: Dùng để sử dụng cho các **pod** chuyện vs nhau
    + Container-runtime: 


![_config.yml]({{ site.baseurl }}/images/nodeport-05.png)