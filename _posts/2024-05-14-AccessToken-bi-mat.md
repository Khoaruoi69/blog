---
layout: post
title: Đồng bộ và không đồng bộ
categories: Thao-luan
---

#### Động bộ là gì ???

- Phải tồn tại 2 đối tượng trở lên. Cộng tác với nhau (VD: đồng bộ tin nhắn zalo) 
- Phải làn tuần tự từng bước.
- Vậy 1 sự việc được diễn ra thì các việc khác làm gì ??? Trong khi chờ đợi.
- Nó ở trạng thái chờ đợi. Nó phải dựa vào sự kiện nào đó phía trước 

- Nó được sử dụng trong cuộc sống đó là xếp hàng mua vé
- Vận dụng logic: Tại sao phải vận dụng xếp hàng. Nó có lợi ích gì ? Nó được vận dụng khi ít tài nguyên, nhưng nhu cầu cao 
(VD: 1 CUP và nhiều Request ). 

==> Đồng bộ là sau khi thực hiện 1 lần, lần tiếp theo cần chờ đợi và phối hợp với điều vừa xảy ra, rồi mới xử lý

![_config.yml]({{ site.baseurl }}/images/dong-bo-1.png)

- ĐỒng bộ nhất định tại phạm vi chi tiết cụ thể

### Không đồng bộ 

- Tốc độ khác nhau
- Vì khác nhau về tốc đô nên bất đồng bộ
- Diễn ra cùng lúc nhưng ko đụng gì tới nhau, kết thúc làm việc riêng
- Request bị lỗi, chậm hay gì thì phải chợ đợi. ĐỒng bộ bất lợi 

==> Việc không đồng bộ có nghĩa chúng độc lập với nhau và kết thúc làm việc riêng trong khi chờ đợi một sự kiện xảy ra
ko cần đợi sự kiện hoàn thành rồi mới hoạt động trở lại.

**Lưu ý**

- Nhiều thứ đây cái mà để chúng ta hiểu về đồng bộ và ko đồng bộ. Nhiều luồn cũng là nhiều thứ, nhiều thread cũng là nhiều thứ,
mà nhiều process, nhiều method, nhiều CPU ... cũng là nhiều thứ