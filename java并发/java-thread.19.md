<!-- Java中的13个原子操作类 -->

Java从JDK 1.5开始提供了java.util.concurrent.atomic包,一共包含13个类，属于4种类型的原子更新方式，分别是
1. 原子更新基本类型
2. 原子更新数组
3. 原子更新引用
4. 原子更新属性（字段）

Atomic包里的类基本都是使用Unsafe实现的包装类

# 原子更新基本类型类

Atomic包提供了以下3个类
1. AtomicBoolean：原子更新布尔类型。
2. AtomicInteger：原子更新整型。
3. AtomicLong：原子更新长整

以AtomicIntege为例,常用方法如下

```java
//以原子方式将输入的数值与实例中的值（AtomicInteger里的value）相加，并返回结果。
int addAndGet（int delta）
//update）：如果输入的数值等于预期值，则以原子方式将该值设置为输入的值。
boolean compareAndSet（int expect，int update）
//以原子方式将当前值加1，注意，这里返回的是自增前的值
int getAndIncrement()
//最终会设置成newValue，使用lazySet设置值后，可能导致其他线程在之后的一小段时间内还是可以读到旧的值
void lazySet（int newValue）
//以原子方式设置为newValue的值，并返回旧值
int getAndSet（int newValue）
```


# 原子更新数组

Atomic包提供了以下3个类
1. AtomicIntegerArray：原子更新整型数组里的元素。
2. AtomicLongArray：原子更新长整型数组里的元素。
3. AtomicReferenceArray：原子更新引用类型数组里的元素。

以AtomicIntegerArray为例，其主要是提供原子的方式更新数组里的整型，常用方法如下
```java
//以原子方式将输入值与数组中索引i的元素相加。
int addAndGet（int i，int delta）：
//如果当前值等于预期值，则以原子方式将数组位置i的元素设置成update值。
boolean compareAndSet（int i，int expect，int update）
```

# 原子更新引用类型

Atomic包提供了以下3个类
1. AtomicReference：原子更新引用类型。
2. AtomicReferenceFieldUpdater：原子更新引用类型里的字段。
3. AtomicMarkableReference：原子更新带有标记位的引用类型

# 原子更新字段类

Atomic包提供了以下3个类
1. AtomicIntegerFieldUpdater：原子更新整型的字段的更新器。
2. AtomicLongFieldUpdater：原子更新长整型字段的更新器。
3. AtomicStampedReference：原子更新带有版本号的引用类