# maven依赖

```
<dependency>
  <groupId>org.mockito</groupId>
  <artifactId>mockito-core</artifactId>
  <version>2.25.1</version>
</dependency>
```
静态导入工具包，方便使用
```
import static org.mockito.Mockito.*;
```
# Stub(桩/存根)

**桩**：用来替换一部分功能的程序段。桩程序可以用来模拟已有程序的行为（比如一个远端机器的过程）或是对将要开发的代码的一种临时替代

如
```java
# 建立一个LinkedList类型的mock对线
 LinkedList mockedList = mock(LinkedList.class);
# 存根
 when(mockedList.get(0)).thenReturn("first");

# 存根后，当执行mockedList.get(0)方法时，就返回定义的值
  System.out.println(mockedList.get(0));
 ```

# Mock对象的创建

方式一：利用Class文件进行Mock创建
```java
 mock(SomeClass.class);
```

方式二：使用注解
```java
    //首先需要调用初始化方法，将测试类作为参数传入
    @Before
    public void init(){
        MockitoAnnotations.initMocks(this);
    }
    //需要模拟化的类上使用@Mock注解
    @Mock
    LinkedList mockedList;
  
```


 # 论证参数匹配

Mockito提供的匹配类: ArgumentMatchers
```java
  LinkedList mockedList = mock(LinkedList.class);
        // anyInt():ArgumentMatchers中的方法，表示参数可以是任何整数
        when(mockedList.get(anyInt())).thenReturn("element");
        System.out.println(mockedList.get(999));

        //isValid(): 用户自定义的匹配方法，返回自定义的匹配器实现
        //使用用户自定义的参数匹配方式
        when(mockedList.contains(argThat(isValid()))).thenReturn("element");

        //可以使用lambd表达式
        //验证如何长度大于5的字符串
        verify(mockedList).add(argThat(someString -> someString.length() > 5));
```

需要注意的是，如果使用了参数匹配器，则所有参数都必须使用匹配器
```java
//正确，anyInt(), anyString(), eq()都是匹配器
   verify(mock).someMethod(anyInt(), anyString(), eq("third argument"));
   //above is correct - eq() is also an argument matcher

//错误，"third argument"并不是匹配器
   verify(mock).someMethod(anyInt(), anyString(), "third argument");
 
 ```

# 验证调用次数

 ```java
    LinkedList mockedList = mock(LinkedList.class);

    //using mock
    mockedList.add("once");

    mockedList.add("twice");
    mockedList.add("twice");

    mockedList.add("three times");
    mockedList.add("three times");
    mockedList.add("three times");

    //默认验证一次，如下代码效果相同
    verify(mockedList).add("once");
    verify(mockedList, times(1)).add("once");

    //验证多次
    verify(mockedList, times(2)).add("twice");
    verify(mockedList, times(3)).add("three times");

    //验证从没执行过的
    verify(mockedList, never()).add("never happened");

    //验证至多/至少_次
    verify(mockedList, atLeastOnce()).add("three times");
    verify(mockedList, atLeast(2)).add("three times");
    verify(mockedList, atMost(5)).add("three times");
```


# 按顺序验证

验证一：单个Mock
```java
List singleMock = mock(List.class);

singleMock.add("was added first");
singleMock.add("was added second");

//为mock对线创建InOrder
InOrder inOrder = inOrder(singleMock);

//验证执行顺序，以下两个方法顺序互换，则报错
inOrder.verify(singleMock).add("was added first");
inOrder.verify(singleMock).add("was added second");
```

验证二：多个Mock
```java
    //创建对各Mock对线
    List secondMock = mock(List.class);
    List firstMock = mock(List.class);


    firstMock.add("was called first");
    secondMock.add("was called second");

    ////为多个Mock对线创建InOrder
    InOrder inOrder = inOrder(firstMock, secondMock);

    //将确保在secondMock.add之前调用firstMock.add
    inOrder.verify(firstMock).add("was called first");
    inOrder.verify(secondMock).add("was called second");
```

#  查找冗余的调用

个人理解：verifyNoMoreInteractions用于验证Mock方法调用后是否有验证，而如果只调用了方法啊，但没有验证的话，表明这个方法还处于**交互**状态。
```java
//using mocks
mockedList.add("one");
mockedList.add("two");

verify(mockedList).add("one");

//following verification will fail
// 下面的验证将会失败,mockedList.add("two");还处于交互状态
verifyNoMoreInteractions(mockedList);
```


# 确保在模拟上从未发生过互动

verifyZeroInteractions就是调用了verifyNoMoreInteractions方法，只是语义不同而已
```java

 //using mocks - only mockOne is interacted
 mockOne.add("one");

 //ordinary verification
 verify(mockOne).add("one");

 //verify that other mocks were not interacted
 verifyZeroInteractions(mockTwo, mockThree);
 ```




#  连续调用（迭代器式存根）

```java
LinkedList mockedList = mock(LinkedList.class);

when(mockedList.add("some arg"))，
        .thenReturn(false)//thenReturn的返回类型与add返回类型相同
        .thenReturn(true)
        .thenReturn(true);

//第一次调用 输出false
System.out.println(mockedList.add("some arg"));

//第二次调用 输出true
System.out.println(mockedList.add("some arg"));

//第三次调用 输出true，如果接下来还有嗲用，继续执行最后一个存根
System.out.println(mockedList.add("some arg"));
```

此外，还可以简写
```
when(mockedList.add("some arg"))
        .thenReturn(false, true, false);
```

**thenReturn()**  使用具有相同匹配器或参数的多个存根而不是链接调用，则每个存根将覆盖前一个存根

# 使用回调函数进行存根

使用thenReturn()或thenThrow()基本可以满足测试需求，但还可以通过通用的**Answer**接口来实现复杂的存根
```java
 when(mock.someMethod(anyString())).thenAnswer(
     new Answer() {
         public Object answer(InvocationOnMock invocation) {
             Object[] args = invocation.getArguments();
             Object mock = invocation.getMock();
             return "called with arguments: " + Arrays.toString(args);
         }
 });

 //Following prints "called with arguments: [foo]"
 System.out.println(mock.someMethod("foo"));
 ```

 # doReturn()| doThrow()| doAnswer()| doNothing()| doCallRealMethod()一系列方法

 在存根为void时使用,如下使用异常对void方法进行存根

```java
    LinkedList mockedList = mock(LinkedList.class);
    //doXxxx().when().someMethod()
    doThrow(new RuntimeException()).when(mockedList).clear();

    //将会 throws RuntimeException:
    mockedList.clear();
 
```

# 监视实例对象

可以创建创建实例对象的**Spy**,当你使用**Spy**时，会调用真正的方法;但spy并不会将调用委托给实例对象，而是创建了它的副本。

```java
//创建实例
List list = new LinkedList();
//创建实例的spy
List spy = spy(list);

//存根
when(spy.size()).thenReturn(100);

//使用spy来使用真正的add()方法
spy.add("one");
spy.add("two");

//prints "one"
System.out.println(spy.get(0));

//size()输出存根：100
//如果不size()方法没有存根，就会调用实例对象的方法
System.out.println(spy.size());

//spy也可以验证
verify(spy).add("one");
verify(spy).add("two");

```

使用**Spy** 应该考虑与**doXxxx**联合使用
```java
   List list = new LinkedList();
   List spy = spy(list);

   //错误，spy.get(0)抛出IndexOutOfBoundsException
   when(spy.get(0)).thenReturn("foo");

   //必须使用doXxxx进行存根
   doReturn("foo").when(spy).get(0);
```

# 真正的部分模拟(1.8)


# 设置未存根方法的默认返回值(1.7)

```

Foo mock = mock(Foo.class, Mockito.RETURNS_SMART_NULLS);
Foo mockTwo = mock(Foo.class, new YourOwnAnswer());
 
 ```

# 捕获断言的参数(1.8)

<!-- ```java
ArgumentCaptor<Person> argument = ArgumentCaptor.forClass(Person.class);
verify(mock).doSomething(argument.capture());
assertEquals("John", argument.getValue().getName());
 ``` -->

# 重置模拟（1.8）

几乎用不到

```
  List mock = mock(List.class);
   when(mock.size()).thenReturn(10);
   mock.add(1);

   reset(mock);//使用后mock会忘记所有的交互和存根
```

# 行为驱动开发的别名(1.8)

行为驱动开发编写测试的样式使用**given**,**when**,**then**作为测试方法的基本部分

# 可序列化的模拟(1.8.1)

# @Captor， @Spy， @InjectMocks(1.8.3)

# 超时验证(1.8.5)

可用于并发条件下的测试
```
    //在100毫秒内满足验证就退出
   verify(mock, timeout(100)).someMethod();
   //与上一句等效
   verify(mock, timeout(100).times(1)).someMethod();
```