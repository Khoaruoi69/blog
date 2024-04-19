---
layout: post
title: Transaction
categories: Java-8
---


### Transaction:

Transaction thường được dùng để nhóm các thao tác dữ liệu lại với nhau thành một đơn vị logic, sao cho các thao tác này **được thực hiện hoàn toàn**
hoặc **không được thực hiện gì cả.**

Một transaction có hai thuộc tính quan trọng: **Tính nhất quán ( Consistency)** và **tính bền vững (durability)**.

+ **Tính nhất quán (Consistency):** Đảm bảo rằng dữ liệu sẽ luôn ở một trạng thái hợp lý sau khi giao dịch hoàn tất. Nói cách khác, dữ liệu không bị mất
tính nhất quán giữa các thao tác trong *giao dịch.*

+ **Tính bền vững (Durability):** Đảm bảo rằng các thay đổi trong giao dịch sẽ được lưu trữ vĩnh viễn, kể cả trong trường hợp lỗi hệ thống hoặc điện toán.

Các giao dịch thường tuân theo nguyên tắc ACID:

+ **Atomicity (Nguyên tố ):** Mọi thao tác trong giao dịch phải **được thực hiện** hoặc **Không thực hiện gì cả**. Không có trạng thái trung gian.

+ **Consistency (Nhất quán):** Dữ liệu phải ở trong một trạng thái hợp lý trước và sau khi giao dịch thực hiện.

+ **Isolation (Độc lập):** Giao dịch phải được thực hiện độc lập với các giao dịch khác, để tránh các vấn đề như đọc ghi mâu thuẫn.

+ **Durability (Bền vững):** Các thay đổi trong giao dịch phải được lưu trữ vĩnh viễn ngay cả trong trường hợp mất điện hoặc bị lỗi hệ thống.


### Ví dụ: 

#### 1. Chuyển tiền ngân hàng ( Atomicity và Consistency):

Trong một hệ thống ngân hàng, khi một người gửi tiền từ tài khoản của mình đến tài khoản của người khác, giao dịch này phải đảm bảo tính nguyên tố và tính nhất quán.

Ví dụ:

+ Người A muốn chuyển $100 từ tài khoản của mình đến tài khoản của người B.

+ Hệ thống ngân hàng sẽ thực hiện hai thao tác: giảm số tiền trong tài khoản của A và tăng số tiền trong tài khoản của B.

+ Nếu một trong hai thao tác thất bại (ví dụ: tài khoản của A không đủ tiền), cả hai thao tác đều phải bị rollback, để đảm bảo tính nguyên tố.

+ Sau khi giao dịch hoàn tất, số dư trong cả hai tài khoản phải phản ánh đúng số tiền đã được chuyển đi và nhận.

```java
@Service
public class BankService {

    @Autowired
    private AccountRepository accountRepository;

    @Transactional
    public void transferMoney(Long senderAccountId, Long receiverAccountId, double amount) {
        Account sender = accountRepository.findById(senderAccountId).orElseThrow(() -> new RuntimeException("Sender account not found"));
        Account receiver = accountRepository.findById(receiverAccountId).orElseThrow(() -> new RuntimeException("Receiver account not found"));

        if (sender.getBalance() < amount) {
            throw new RuntimeException("Insufficient funds");
        }

        sender.setBalance(sender.getBalance() - amount);
        receiver.setBalance(receiver.getBalance() + amount);

        accountRepository.save(sender);
        accountRepository.save(receiver);
    }
}

```

#### 2. Quá trình đặt hàng trực tuyến (Isolation)

Trong một ứng dụng thương mại điện tử, khi một khách hàng đặt hàng trực tuyến, giao dịch này phải được thực hiện mà không bị ảnh hưởng bởi các đơn đặt hàng khác cùng được thực hiện đồng thời.

Ví dụ:

+ Khách hàng A đặt hàng một sản phẩm.

+ Trong khi đó, khách hàng B cũng đặt hàng một sản phẩm.

+ Dù cả hai đơn đặt hàng đều đang được xử lý cùng một lúc, giao dịch của A không nên thấy được thông tin đơn đặt hàng của B và ngược lại.

+ Điều này đảm bảo tính độc lập giữa các giao dịch.

```java
@Service
public class OrderService {

    @Autowired
    private OrderRepository orderRepository;

    @Transactional(isolation = Isolation.REPEATABLE_READ)
    public void placeOrder(Order order) {
        // Xử lý đặt hàng
        orderRepository.save(order);
    }
}

```
#### 3.Thực Hiện Thay Đổi Trong Cơ Sở Dữ Liệu (Durability):

Khi một hệ thống thực hiện các thay đổi trong cơ sở dữ liệu như thêm, sửa, xóa dữ liệu, các thay đổi này phải được lưu trữ vĩnh viễn ngay cả khi có lỗi hệ thống.

Ví dụ:

+ Một hệ thống quản lý nhân viên cho phép quản trị viên thêm mới nhân viên vào cơ sở dữ liệu.

+ Khi quản trị viên thêm một nhân viên mới, thông tin về nhân viên này phải được lưu trữ đúng cách trong cơ sở dữ liệu.

+ Ngay cả khi có một lỗi hệ thống xảy ra sau khi thông tin của nhân viên mới đã được lưu, thông tin này vẫn phải được bảo toàn để không mất dữ liệu.

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeRepository employeeRepository;

    @Transactional
    public void addEmployee(Employee employee) {
        // Thêm mới nhân viên vào cơ sở dữ liệu
        employeeRepository.save(employee);
    }
}

```

**Trong ví dụ trên:**

+ @Transactional được sử dụng để bắt đầu một giao dịch.

+ Trong trường hợp 1, hai thao tác save() được gọi trong cùng một phương thức, đảm bảo rằng nếu một thao tác thất bại, cả hai đều sẽ bị rollback.

+ Trong trường hợp 2, isolation level được thiết lập thành REPEATABLE_READ để đảm bảo tính độc lập giữa các đơn đặt hàng.

+ Trong trường hợp 3, mọi thay đổi được thực hiện trong phương thức sẽ được tự động commit sau khi phương thức hoàn thành, đảm bảo tính bền vững của dữ liệu.


