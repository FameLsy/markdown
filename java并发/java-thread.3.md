

# Lock 接口

Java SE 5之后，并发包中新增了Lock接口,在使用时需要显式地获取和释放锁。

>注意
>不要将获取锁的过程写在try块中，因为如果在获取锁（自定义锁的实现）时发生了异常，异常抛出的同时，也会导致锁无故释放

Lock接口提供的synchronized关键字不具备的主要特性

![thread4](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread4.png)

Lock的API

![thread5](https://raw.githubusercontent.com/FameLsy/Images/master/thread/thread5.png)
