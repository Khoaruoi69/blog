---
layout: post
title: Sử Dụng kubectl logs
categories: Kubernetes
---

### Kubectl logs

- xem logs ở trạng thái hiện tại: kubectl logs app1
- xem logs ở trạng thái liên tục: kubectl logs app1 -f 
- Nếu pod có nhiều container: kubectl logs app1 -c app2 -f 
- xem thông tin của container: kubectl describe pod app1 