---
layout: post
title: Multi-thread
categories: Multi-thread
---

### Giới thiệu


Link: https://gpcoder.com/3484-lap-trinh-da-luong-trong-java-java-multi-threading/

**Thread là gì ?**

- Thread (luồng) về cơ bản là một tiến trình con (sub-process). Một đơn vị xử lý nhỏ nhất của máy tính có thể thực hiện một công việc riêng biệt. Trong Java, các luồng được quản lý bởi máy ảo Java (JVM)

**Multi-thread**

- Multi-thread (đa luồng) là một tiến trình thực hiện nhiều luồng đồng thời. Một ứng dụng Java ngoài luồng chính có thể có các luồng khác thực thi đồng thời làm ứng dụng chạy nhanh và hiệu quả hơn.

**Đa nhiệm (multitasking)**

- Multitasking: Là khả năng chạy đồng thời một hoặc nhiều chương trình cùng một lúc trên một hệ điều hành. Hệ điều hành quản lý việc này và sắp xếp lịch phù hợp cho các chương trình đó. Ví dụ, trên hệ điều hành Windows chúng ta có làm việc đồng thời với các chương trình khác nhau như: Microsoft Word, Excel, Media Player, …

- Chúng ta sử dụng đa nhiệm để tận dụng tính năng của CPU.

- Đa nhiệm có thể đạt được bằng hai cách:

**Đa nhiệm dựa trên đơn tiến trình (Process) – Đa tiến trình (Multiprocessing).**

+ Mỗi tiến trình có địa chỉ riêng trong bộ nhớ, tức là mỗi tiến trình phân bổ vùng nhớ riêng biệt.
+ Tiến trình là nặng.
+ Sự giao tiếp giữa các tiến trình có chi phí cao.
+ Chuyển đổi từ tiến trình này sang tiến trình khác đòi hỏi thời gian để đăng ký việc lưu và tải các bản đồ bộ nhớ, các danh sách cập nhật, …

**Đa nhiệm dựa trên luồng (Thread) – Đa luồng (MultiThreading).**

+ Các luồng chia sẻ không gian địa chỉ ô nhớ giống nhau.
+ Luồng là nhẹ.
+ Sự giao tiếp giữa các luồng có chi phí thấp.


Đa tiến trình (multiprocessing) và đa luồng (multithreading) cả hai được sử dụng để tạo ra hệ thống đa nhiệm (multitasking). Nhưng chúng ta sử dụng đa luồng nhiều hơn đa tiến trình bởi vì các luồng chia sẻ một vùng bộ nhớ chung. Chúng không phân bổ vùng bộ nhớ riêng biệt để tiết kiệm bộ nhớ, và chuyển đổi ngữ cảnh giữa các luồng mất ít thời gian hơn tiến trình.

![_config.yml]({{ site.baseurl }}/images/thread-01.png)

### 1.3 Ưu điểm của luồng

+ Nó không chặn người sử dụng vì các luồng là độc lập và bạn có thể thực hiện nhiều công việc cùng một lúc.
+ Mỗi luồng có thể dùng chung và chia sẻ nguồn tài nguyên trong quá trình chạy, nhưng có thể thực hiện một cách độc lập.
+ Luồng là độc lập vì vậy nó không ảnh hưởng đến luồng khác nếu ngoại lệ xảy ra trong một luồng duy nhất.
+ Có thể thực hiện nhiều hoạt động với nhau để tiết kiệm thời gian. Ví dụ một ứng dụng có thể được tách thành : luồng chính chạy giao diện người dùng và các luồng phụ nhiệm gửi kết quả xử lý đến luồng chính.

### 1.4 Nhược điểm của đa luồng

+ Càng nhiều luồng thì xử lý càng phức tạp.
+ Xử lý vấn đề về tranh chấp bộ nhớ, đồng bộ dữ liệu khá phức tạp.
+ Cần phát hiện tránh các luồng chết (dead lock), luồng chạy mà không làm gì trong ứng dụng cả.

*Deadlock (Khoá chết) là gì? Deadlock xảy ra khi 2 tiến trình đợi nhau hoàn thành, trước khi chạy. Kết quả của quá trình là cả 2 tiến trình không bao giờ kết thúc.*

### 2. Vòng đời (các trạng thái) của một Thread trong java

![_config.yml]({{ site.baseurl }}/images/thread-02.png)

+ **NEW :** Đây là trạng thái khi luồng vừa được khởi tạo bằng phương thức khởi tạo của lớp Thread nhưng chưa được start(). Ở trạng thái này, luồng được tạo ra nhưng chưa được cấp phát tài nguyên và cũng chưa chạy. Nếu luồng đang ở trạng thái này mà ta gọi các phương thức ép buộc stop,resume,suspend … sẽ là nguyên nhân sảy ra ngoại lệ IllegalThreadStateException .

+ **RUNNABLE :** Sau khi gọi phương thức start() thì luồng test đã được cấp phát tài nguyên và các lịch điều phối CPU cho luồng test cũng bắt đầu có hiệu lực. Ở đây, chúng ta dùng trạng thái là Runnable chứ không phải Running, vì luồng không thực sự luôn chạy mà tùy vào hệ thống mà có sự điều phối CPU khác nhau.

+ **WAITING :** Thread chờ không giới hạn cho đến khi một luồng khác đánh thức nó.

+ **TIMED_WAITING :** Thread chờ trong một thời gian nhất định, hoặc là có một luồng khác đánh thức nó.

+ **BLOCKED:** Đây là 1 dạng của trạng thái “Not Runnable”, là trạng thái khi Thread vẫn còn sống, nhưng hiện tại không được chọn để chạy. Thread chờ một monitor để unlock một đối tượng mà nó cần.

+ **TERMINATED :** Một thread ở trong trạng thái terminated hoặc dead khi phương thức run() của nó bị thoát.

### 3. Cách tạo luồng trong Java

- Trong java ta có thể tạo ra một luồng bằng một trong hai cách sau: tạo 1 đối tượng của lớp được extend từ class Thread hoặc implements từ interface Runnable.

#### 3.1 Tạo luồng bằng cách extend từ lớp Thread

Để tạo luồng bằng cách tạo lớp kế thừa từ lớp Thread, ta phải làm các công việc sau :

+ Khai báo 1 lớp mới kế thừa từ lớp Thread
+ Override lại phương thức run ở lớp này, những gì trong phương thức run sẽ được thực thi khi luồng bắt đầu chạy. Sau khi luồng chạy xong tất cả các câu lệnh trong phương thức run thì luồng cũng tự hủy.
+ Tạo 1 thể hiện (hay 1 đối tượng) của lớp ta vừa khai báo.
+ Sau đó gọi phương thức start() của đối tượng này để bắt đầu thực thi luồng.

```java
package com.gpcoder.simple;
 
public class TheadSimple extends Thread {
    public void run() {
        System.out.println("thread is running...");
    }
 
    public static void main(String args[]) {
        TheadSimple t1 = new TheadSimple();
        t1.start();
    }
}
```

**Note**

- Tuy ta khai báo những công việc cần làm của luồng trong phương thức run() nhưng khi thực thi luồng ta phải gọi phương thức start(). Vì đây là phương thức đặc biệt mà java xây dựng sẵn trong lớp Thread, phương thức này sẽ cấp phát tài nguyên cho luồng mới rồi chạy phương thức run() ở luồng này. Vì vậy, nếu ta gọi phương thức run() mà không gọi start() thì cũng tương đương với việc gọi 1 phương thức của 1 đối tượng bình thường và phương thức vẫn chạy trên luồng mà gọi phương thức chứ không chạy ở luồng mới tạo ra, nên vẫn chỉ có 1 luồng chính làm việc chứ ứng dụng vẫn không phải là đa luồng.

- Sau khi start một thread, nó không bao giờ có thể được start lại. Nếu bạn làm như vậy, một ngoại lệ IllegalThreadStateException sẽ xảy ra.

### 3.2 Tạo luồng bằng cách implement từ Interface Runnable

Để tạo luồng bằng cách hiện thực từ Interface Runnable, ta phải làm các công việc sau :

+ Khai báo 1 lớp mới implements từ Interface Runnable
+ Hiện thực phương thức run() ở lớp này, những gì trong phương thức run() sẽ được thực thi khi luồng bắt đầu chạy. Sau khi luồng chạy xong tất cả các câu lệnh trong phương thức run thì luồng cũng tự hủy.
+ Tạo 1 thể hiện (hay 1 đối tượng) của lớp ta vừa khai báo. (VD : Tên đối tượng là r1)
+ Tạo 1 thể hiện của lớp Thread bằng phương thức khởi tạo : Thread(Runnable target)

   - Runnable target: Là 1 đối tượng thuốc lớp được implements từ giao diện Runnable.

   - Ví dụ: Thread t1 = new Thread(r1);

Gọi phương thức start() của đối tượng t1.


```java
package com.gpcoder.simple;
 
public class RunnableSimple implements Runnable {
    public void run() {
        System.out.println("thread is running...");
    }
 
    public static void main(String args[]) {
        RunnableSimple runable = new RunnableSimple();
        Thread t1 = new Thread(runable);
        t1.start();
    }
}
```

#### 3.3 Khi nào implements từ interface Runnable?

+ Cách hay được sử dụng và được yêu thích là dùng interface Runnable, bởi vì nó không yêu cầu phải tạo một lớp kế thừa từ lớp Thread. Trong trường hợp ứng dụng thiết kế yêu cầu sử dụng đa kế thừa, chỉ có interface mới có thể giúp giải quyết vấn đề. Ngoài ra, Thread Pool rất hiểu quả và có thể được cài đặt, sử dụng rất hơn giản.
+ Trong trường hợp còn lại ta có thể kế thừa từ lớp Thread.

### 3.4 Ví dụ minh họa sử dụng đa luồng

#### 3.4.1 Ví dụ Tạo luồng bằng cách extend từ class Thread

Tạo luồng extend từ class Thead

```java
package com.gpcoder.flow;
 
public class ThreadDemo extends Thread {
    private Thread t;
    private String threadName;
 
    ThreadDemo(String name) {
        threadName = name;
        System.out.println("Creating " + threadName);
    }
 
    @Override
    public void run() {
        System.out.println("Running " + threadName);
        try {
            for (int i = 4; i > 0; i--) {
                System.out.println("Thread: " + threadName + ", " + i);
                // Let the thread sleep for a while.
                Thread.sleep(50);
            }
        } catch (InterruptedException e) {
            System.out.println("Thread " + threadName + " interrupted.");
        }
        System.out.println("Thread " + threadName + " exiting.");
    }
 
    public void start() {
        System.out.println("Starting " + threadName);
        if (t == null) {
            t = new Thread(this, threadName);
            t.start();
        }
    }
 
}
```

Chương trình sử dụng đa luồng:

```java
package com.gpcoder.flow;
 
public class ThreadDemoTest {
    public static void main(String args[]) {
        System.out.println("Main thread running... ");
 
        ThreadDemo T1 = new ThreadDemo("Thread-1-HR-Database");
        T1.start();
 
        ThreadDemo T2 = new ThreadDemo("Thread-2-Send-Email");
        T2.start();
 
        System.out.println("==&gt; Main thread stopped!!! ");
    }
}
```

Kết quả thực thi chương trình trên:

```java
Main thread running...
Creating Thread-1-HR-Database
Starting Thread-1-HR-Database
Creating Thread-2-Send-Email
Starting Thread-2-Send-Email
==&amp;amp;gt; Main thread stopped!!!
Running Thread-1-HR-Database
Running Thread-2-Send-Email
Thread: Thread-2-Send-Email, 4
Thread: Thread-1-HR-Database, 4
Thread: Thread-1-HR-Database, 3
Thread: Thread-2-Send-Email, 3
Thread: Thread-2-Send-Email, 2
Thread: Thread-1-HR-Database, 2
Thread: Thread-2-Send-Email, 1
Thread: Thread-1-HR-Database, 1
Thread Thread-2-Send-Email exiting.
Thread Thread-1-HR-Database exiting.
```

Kết quả chương trình trên được giải thích thông qua hình bên dưới:

![_config.yml]({{ site.baseurl }}/images/thread-03.png)

#### 3.4.2 Ví dụ Tạo luồng bằng cách implement từ Interface Runnable

Tạo luồng implement từ Interface Runnable

```java
package com.gpcoder.flow;
 
class RunnableDemo implements Runnable {
    private Thread t;
    private String threadName;
 
    RunnableDemo(String name) {
        threadName = name;
        System.out.println("Creating " + threadName);
    }
 
    @Override
    public void run() {
        System.out.println("Running " + threadName);
        try {
            for (int i = 4; i > 0; i--) {
                System.out.println("Thread: " + threadName + ", " + i);
                // Let the thread sleep for a while.
                Thread.sleep(50);
            }
        } catch (InterruptedException e) {
            System.out.println("Thread " + threadName + " interrupted.");
        }
        System.out.println("Thread " + threadName + " exiting.");
    }
 
    public void start() {
        System.out.println("Starting " + threadName);
        if (t == null) {
            t = new Thread(this, threadName);
            t.start();
        }
    }
 
}
```

Chương trình sử dụng đa luồng

```java
package com.gpcoder.flow;
 
public class RunnableDemoTest {
    public static void main(String args[]) {
        System.out.println("Main thread running... ");
 
        RunnableDemo R1 = new RunnableDemo("Thread-1-HR-Database");
        R1.start();
 
        RunnableDemo R2 = new RunnableDemo("Thread-2-Send-Email");
        R2.start();
 
        System.out.println("==&gt; Main thread stopped!!! ");
    }
}
```

Kết quả thực thi chương trình trên:

```java
Main thread running...
Creating Thread-1-HR-Database
Starting Thread-1-HR-Database
Creating Thread-2-Send-Email
Starting Thread-2-Send-Email
==&amp;amp;gt; Main thread stopped!!!
Running Thread-1-HR-Database
Running Thread-2-Send-Email
Thread: Thread-1-HR-Database, 4
Thread: Thread-2-Send-Email, 4
Thread: Thread-1-HR-Database, 3
Thread: Thread-2-Send-Email, 3
Thread: Thread-1-HR-Database, 2
Thread: Thread-2-Send-Email, 2
Thread: Thread-1-HR-Database, 1
Thread: Thread-2-Send-Email, 1
Thread Thread-1-HR-Database exiting.
Thread Thread-2-Send-Email exiting.
```

![_config.yml]({{ site.baseurl }}/images/thread-04.png)

### 4. Các phương thức của lớp Thread thường hay sử dụng

- **suspend() :** Đây là phương thức làm tạm dừng hoạt động của 1 luồng nào đó bằng các ngưng cung cấp CPU cho luồng này. Để cung cấp lại CPU cho luồng ta sử dụng phương thức resume(). Cần lưu ý 1 điều là ta không thể dừng ngay hoạt động của luồng bằng phương thức này. Phương thức suspend() không dừng ngay tức thì hoạt động của luồng mà sau khi luồng này trả CPU về cho hệ điều hành thì không cấp CPU cho luồng nữa

- **resume() :** Đây là phương thức làm cho luồng chạy lại khi luồng bị dừng do phương thức suspend() bên trên. Phương thức này sẽ đưa luồng vào lại lịch điều phối CPU để luồng được cấp CPU chạy lại bình thường

- **stop() :** Luồng này sẽ kết thúc phương thức run() bằng cách ném ra 1 ngoại lệ ThreadDeath, điều này cũng sẽ làm luồng kết thúc 1 cách ép buộc. Nếu giả sử, trước khi gọi stop() mà luồng đang nắm giữa 1 đối tượng nào đó hoặc 1 tài nguyên nào đó mà luồng khác đang chờ thì có thể dẫn tới việc sảy ra deadlock

- **destroy() :** dừng hẳn luồng.

- **isAlive() :** Phương thức này kiểm tra xem luồng còn active hay không. Phương thức sẽ trả về true nếu luồng đã được start() và chưa rơi vào trạng thái dead. Nếu phương thức trả về false thì luồng đang ở trạng thái “New Thread” hoặc là đang ở trạng thái “Dead”

- **yeild() :** Hệ điều hành đa nhiệm sẽ phân phối CPU cho các tiến trình, các luồng theo vòng xoay. Mỗi luồng sẽ được cấp CPU trong 1 khoảng thời gian nhất định, sau đó trả lại CPU cho hệ điều hành (HĐH), HĐH sẽ cấp CPU cho luồng khác. Các luồng sẽ nằm chờ trong hàng đợi Ready để nhận CPU theo thứ tự. Java có cung cấp cho chúng ta 1 phương thức khá đặc biệt là yeild(), khi gọi phương thức này luồng sẽ bị ngừng cấp CPU và nhường cho luồng tiếp theo trong hàng chờ Ready. Luồng không phải ngưng cấp CPU như suspend mà chỉ ngưng cấp trong lần nhận CPU đó mà thôi.

- **sleep(long)** : tạm dừng luồng trong một khoảng thời gian millisecond.
- **join() :** thông báo rằng hãy chờ thread này hoàn thành rồi thread cha mới được tiếp tục chạy.
- **join(long) :** Thread cha cần phải đợi millisecond mới được tiếp tục chạy, kể từ lúc gọi join(long). Nếu tham số millis = 0 nghĩa là đợi cho tới khi luồng này kết thúc.
- **getName() :** Trả về tên của thread.
- **setName(String name) :** Thay đổi tên của thread.
- **getId() :** Trả về id của thread.
- **getState():** trả về trạng thái của thread.
- **currentThread() :** Trả về tham chiếu của thread đang được thi hành.
- **getPriority() :** Trả về mức độ ưu tiên của thread.
- **setPriority(int) :** Thay đổi mức độ ưu tiên của thread.
- **isDaemon() :** Kiểm tra nếu thread là một luồng Daemon.
- **setDaemon(boolean):** xác định thread là một luồng Daemon hay không.
- **interrupt() :** làm gián đoạn một luồng trong java. Nếu thread nằm trong trạng thái sleep hoặc wait, nghĩa là sleep() hoặc wait() được gọi ra. Việc gọi phương thức interrupt() trên thread đó sẽ phá vỡ trạng thái sleep hoặc wait và ném ra ngoại lệ InterruptedException. Nếu thread không ở trong trạng thái sleep hoặc wait, việc gọi phương thức interrupt() thực hiện hành vi bình thường và không làm gián đoạn thread nhưng đặt cờ interrupt thành true.
- **isInterrupted()** : kiểm tra nếu thread đã bị ngắt.
- **interrupted() :** kiểm tra nếu thread hiện tại đã bị ngắt.

#### 5. Một số thông tin liên quan đến luồng

#### 5.1 Định danh của luồng (ThreadId)

ThreadId là định danh của luồng, nó dùng để phân biệt với các luồng khác cùng tiến trình hoặc cùng tập luồng. Đây là thông số mà máy ảo java tự tạo ra khi ta tạo luồng nên ta không thể sửa đổi cũng như áp đặt thông số này khi tạo luồng. Nhưng ta có thể lấy được nó thông qua phương thức getId() của lớp Thread

#### 5.2 Tên của luồng (ThreadName)

ThreadName là tên của luồng, đây là thuộc tính mà ta có thể đặt hoặc không đặt cho luồng. Nếu ta không đặt cho luồng thì máy ảo java sẽ tự đặt với quy tắc sau: “Thread-” + Thứ tự luồng được tạo ra, bắt đầu từ 0.

#### 5.3 Độ ưu tiên của luồng (Priority)

Như đã nói ở phần trước, mỗi luồng có 1 độ ưu tiên nhất định. Đây sẽ là thông số quyết định mức ưu tiên khi cấp phát CPU cho các luồng.

Trong java, đế đặt độ ưu tiên cho 1 luồng ta dùng phương thức: void setPriority(int newPriority)

+ int newPriority : Là giá trị từ 1 đến 10.

Java có định nghĩa sẵn 3 mức ưu tiên chuẩn như sau:

+ Thread.MIN_PRIORITY (giá trị 01)
+ Thread.NORM_PRIORITY (giá trị 05)
+ Thread.MAX_PRIORITY (giá trị 10)

Để lấy độ ưu tiên của 1 luồng, ta dùng phương thức: int getPriority()

#### 5.4 Ví dụ minh họa

WorkingThread.java

```java
package com.gpcoder.info;
 
public class WorkingThread extends Thread {
    public WorkingThread(String name) {
        super(name);
    }
 
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.printf("Luồng: %s có độ ưu tiên là %d \n", getName(), getPriority());
        }
    }
}
```

ThreadInfoExample.java

```java
package com.gpcoder.info;
 
public class ThreadInfoExample {
 
    public static void main(String[] args) {
        Thread t1 = new WorkingThread("Luồng 1");
        Thread t2 = new WorkingThread("Luồng 2");
        Thread t3 = new WorkingThread("Luồng 3");
 
        System.out.println("ID luồng 1: " + t1.getId());
        System.out.println("ID luồng 2: " + t2.getId());
        System.out.println("ID luồng 3: " + t3.getId());
 
        t1.setPriority(1);
        t2.setPriority(5);
        t3.setPriority(10);
 
        t1.start();
        t2.start();
        t3.start();
    }
 
}
```

Kết quả thực thi chương trình trên:

```java
ID luồng 1: 10
ID luồng 2: 11
ID luồng 3: 12
Luồng: Luồng 2 có độ ưu tiên là 5
Luồng: Luồng 2 có độ ưu tiên là 5
Luồng: Luồng 2 có độ ưu tiên là 5
Luồng: Luồng 2 có độ ưu tiên là 5
Luồng: Luồng 2 có độ ưu tiên là 5
Luồng: Luồng 1 có độ ưu tiên là 1
Luồng: Luồng 3 có độ ưu tiên là 10
Luồng: Luồng 3 có độ ưu tiên là 10
Luồng: Luồng 3 có độ ưu tiên là 10
Luồng: Luồng 3 có độ ưu tiên là 10
Luồng: Luồng 3 có độ ưu tiên là 10
Luồng: Luồng 1 có độ ưu tiên là 1
Luồng: Luồng 1 có độ ưu tiên là 1
Luồng: Luồng 1 có độ ưu tiên là 1
Luồng: Luồng 1 có độ ưu tiên là 1
```

### 6. Sử dụng phương thức sleep()

Phương thức sleep() của lớp Thread được sử dụng để tạm ngừng một thread cho một khoảng thời gian nhất định.

Ví dụ chương trình in ra số từ 1 – 5, tạm ngừng 500 ms trước khi in chữ số tiếp theo.

```java
package com.gpcoder.sleep;
 
public class SleepMethodExample extends Thread {
 
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println(i);
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
 
    public static void main(String args[]) {
        SleepMethodExample t1 = new SleepMethodExample();
        t1.start();
    }
 
}
```

### 7/ Sử dụng join() và join(long)

**join() :** thông báo rằng hãy chờ thread này hoàn thành rồi thread cha mới được tiếp tục chạy.

Ví dụ:

```java
package com.gpcoder.join;
 
public class UsingJoinMethod extends Thread {
 
    public UsingJoinMethod(String name) {
        super(name);
    }
 
    @Override
    public void run() {
        System.out.println(getName());
        for (int i = 1; i <= 5; i++) {
            try {
                System.out.print(i + " ");
                Thread.sleep(300);
            } catch (InterruptedException ie) {
                System.out.println(ie.toString());
            }
        }
        System.out.println();
    }
 
    public static void main(String[] args) throws InterruptedException {
        UsingJoinMethod t1 = new UsingJoinMethod("Thread 1");
        UsingJoinMethod t2 = new UsingJoinMethod("Thread 2");
        t1.start();
        t1.join();
        t2.start();
        System.out.println("Main Thread Finished");
    }
}
```

Thực thi chương trình trên:

```java
Thread 1
1 2 3 4 5
Main Thread Finished
Thread 2
1 2 3 4 5
```

**join(long) :** Thread cha cần phải đợi millisecond mới được tiếp tục chạy, kể từ lúc gọi join(long). Nếu tham số millis = 0 nghĩa là đợi cho tới khi luồng này kết thúc.\

```java
package com.gpcoder.join;
 
public class UsingJoinMethod2 extends Thread {
 
    public UsingJoinMethod2(String name) {
        super(name);
    }
 
    @Override
    public void run() {
        System.out.println(getName());
        for (int i = 1; i <= 5; i++) {
            try {
                System.out.print(i + " ");
                Thread.sleep(300);
            } catch (InterruptedException ie) {
                System.out.println(ie.toString());
            }
        }
        System.out.println();
    }
 
    public static void main(String[] args) throws InterruptedException {
        UsingJoinMethod2 t1 = new UsingJoinMethod2("Thread 1");
        UsingJoinMethod2 t2 = new UsingJoinMethod2("Thread 2");
        t1.start();
 
        // Main Thread phải chờ 450ms mới được tiếp tục chạy.
        // Không nhất thiết phải chờ Thread t1 kết thúc
        t1.join(450);
 
        t2.start();
        System.out.println("Main Thread Finished");
    }
}
```

Thực thi chương trình trên:

```java
Thread 1
1 2 Main Thread Finished
Thread 2
1 3 2 4 3 5 4
5
```
### 8. Xử lý ngoại lệ cho Thread

Phương thức Thread.setDefaultUncaughtExceptionHandler() thiết lập mặc định xử lý khi luồng đột ngột chấm dứt do một ngoại lệ xảy ra mà không có xử lý khác đã được xác định cho luồng đó.

```java
package com.gpcoder.exception;
 
import java.util.Random;
 
public class WorkingThread implements Runnable {
 
    @Override
    public void run() {
        while (true) {
            processSomething();
        }
    }
 
    private void processSomething() {
        try {
            System.out.println("Processing working thread");
            Thread.sleep(500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
 
        Random r = new Random();
        int i = r.nextInt(100);
        if (i &gt; 70) {
            throw new RuntimeException("Simulate an exception was not handled in the thread");
        }
    }
 
}
```

Chương trình minh họa xử lý Thread Exception

```java
package com.gpcoder.exception;
 
public class ThreadExceptionDemo {
 
    public static void main(String[] args) {
        System.out.println("==> Main thread running...");
 
        Thread thread = new Thread(new WorkingThread());
        Thread.setDefaultUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler() {
            @Override
            public void uncaughtException(Thread t, Throwable e) {
                System.out.println("#Thread: " + t);
                System.out.println("#Thread exception message: " + e.getMessage());
            }
        });
 
        thread.start();
        System.out.println("==&amp;gt; Main thread end!!!");
    }
}
```

Thực thi chương trình trên:

```java
==&amp;amp;gt; Main thread running...
==&amp;amp;gt; Main thread end!!!
Processing working thread
Processing working thread
Processing working thread
Processing working thread
Processing working thread
Processing working thread
#Thread: Thread[Thread-0,5,main]
#Thread exception message: Have a problem...

```

Trên đây là những kiến thức cơ bản về đa luồng (Multi-thread) trong java. Chúng ta sẽ tiếp tục tìm hiểu về các vấn đề khác của đa luồng trong Java ở các bài viết tiếp theo. Cám ơn các bạn đã quan tâm và theo dõi bài viết.