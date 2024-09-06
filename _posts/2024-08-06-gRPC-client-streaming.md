---
layout: post
title: gRPC Client streaming
categories: gRPC
---

## Tổng quan

- Với framework gRPC, chúng ta có thể gửi nhiều messages giữa Client và Server thông qua một kết nối TCP duy nhất. Nó được gọi là **Multiplexing**.

- Trong gRPC client streaming, Client có thể gửi nhiều request đến Server. Sau khi Client xác nhận rằng nó đã gửi tất cả các request, Server sẽ gửi lại một response duy nhất cho Client.
