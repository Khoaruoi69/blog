---
layout: post
title: Sử Dụng NodePort Service trên Kubernetes Để Expose Pod
categories: Kubernetes
---

###  NodePort Service 

- get port: kubectl get pods
- get service: kubectl get services ( kubectl get svc)

- hepls expose: kubectl expose --help

- expose port: kubectl expose pods app1 --port=8081 --target-port=8080 --name=service1 --type=NodePort

- describe service port: kubectl get svc service1

- get node: kubectl get nodes 
- xem thong tin chi tiet node: kubectl get nodes -o wide 

- expose duong link minikube: minikube service service1 --url

