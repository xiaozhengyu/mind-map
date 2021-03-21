[TOC]

（出于代码简洁考虑，下文部分方法不进行异常处理）

### 1. 多个线程    同一个对象    synchronized方法

一个对象里面如果有多个synchronized方法，同一时刻内，只要有一个线程调用了其中任意一个synchronized方法，其他线程都只能等待，换而言之，任意时刻只能有一个线程去访问这个类的synchronized方法——<font color = red>**整个对象都被锁了**</font>。

```java
// 现象：总是要等张三发完邮件李四才能发送短信。
public class Lock8Demo {
    public static void main(String[] args){
        Phone phone = new Phone();

        new Thread(()->{phone.sendEmail();},"张三").start();
        TimeUnit.SECONDS.sleep(1); 
        
        new Thread(()->{phone.sendSMS();},"李四").start();
    }
}

class Phone {
    public synchronized void sendEmail() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println(Thread.currentThread().getName() + " 发邮件");
    }

    public synchronized void sendSMS() {
        System.out.println(Thread.currentThread().getName() + " 发短信");
    }
}
```

### 2. 多个线程    同一对象    synchronized方法和普通方法

普通方法的调用与锁无关

```java
// 现象：李四先sayHello，张三才发完邮件
public class Lock8Demo {
    public static void main(String[] args){
        Phone phone = new Phone();

        new Thread(()->{phone.sendEmail();},"张三").start();
        TimeUnit.SECONDS.sleep(1); 
        
        new Thread(()->{phone.sayHello();},"李四").start();
    }
}

class Phone {
    public synchronized void sendEmail() throws InterruptedException {
        TimeUnit.SECONDS.sleep(4);
        System.out.println(Thread.currentThread().getName() + " 发邮件");
    }

    public synchronized void sendSMS() {
        System.out.println(Thread.currentThread().getName() + " 发短信");
    }
    
    public void sayHello(){
        System.out.println(Thread.currentThread().getName() + " sayHello");
    }
}
```

### 3. 多个线程    不同对象    synchronized方法/synchronized方法和普通方法

直接说结论：情况立刻发生变化，不同的对象，不同的锁，互补影响。