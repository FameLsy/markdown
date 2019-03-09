---
title: SpringMVC-请求参数绑定
tgas: 
- SpringMVC-请求参数绑定
categories: 
- SpringMVC
---

什么是请求参数绑定?

一个请求发送到服务器，其所有的参数都将以字符串的形式(Key/value方式)传递到服务器。而在我们的Controller的方法参数中，参数类型可以是基本数据类型，POJO类，集合等类型。

请求参数绑定，就是将请求参数串中的value值获取到之后，再进行类型转换（通过SpringMVC内置的参数解析组件），然后将转换后的值赋值给Controller类中方法的形参，这个过程就是参数绑定

# SpringMVC默认支持的参数类型

SpringMVC默认支持如下参数类型，Controller方法形参中可以随时添加这些类型的参数，处理适配器会自动识别并进行赋值。
1. HttpServletRequest
2. HttpServletResponse
3. HttpSession
3. InputStream、OutputStream
4. Reader、Writer
5. Model/ModelMap

ModelMap继承自LinkedHashMap，Model是一个接口，它们的底层实现都是同一个类（BindingAwareModelMap），作用就是向页面传递数据，相当于Request的作用

通过
```java
model.setAttribute(key, value);
```

# 参数绑定

参数绑定，可以分为
1. 对简单类型的参数绑定
2. 对POJO类型的参数绑定
3. 对包装POJO的参数绑定
4. 使用简单类型数组接收参数
5. 使用POJO类型集合或者数组接收参数
6. 自定义参数绑定

## 对简单类型的参数绑定

简单类型指的就是八大基本数据类型和String类型，有两种绑定方式
1. 直接绑定
2. 注解绑定

### 直接绑定

直接绑定只需要保证请求参数的key与controller方法的参数名称一致即可

如URL如下，带一个参数，key为 "id"
```
http://localhost:8080/xxx/findItem?id=1
```

那么controller方法如下,参数名设置为id即可
```java
@RequestMapping(value = "/findItem")
	public String findItem(Integer id) {
        ...
    }
```
### 通过注解绑定

当请求参数的key与controller方法的参数名称 不 一致时，可以使用@RequestParam注解绑定

如URL如下，带一个参数，key为 "id"
```
http://localhost:8080/xxx/findItem?id=1
```
controller方法如下,参数名设置为itemId,通过@RequestParam,就可以实现注解的绑定
```java
@RequestMapping(value = "/findItem")
	public String findItem(@RequestParam("id:)Integer itemId) {
        ...
    }
```

### RequestParam注解

|属性名|描述|
|--|--|
|value|请求参数名字,将key为value的参数值传入到修饰的cotroller方法参数中|
|required|是否必需，默认是true，表示请求中一定要有相应的参数，否则将报错|
|defaultValue|如果请求中没有value属性对应的key，即值为null时，可以为cotroller方法参数设置默认值|

## 绑定POJO类

想要绑定POJO类，必须满足
1. controller方法的参数类型是 POJO 类型
2. 请求参数名称和 POJO 类的属性名称保持一致

如，url传入参数
1. id = 1
2. name = iphone
3. price
```
http://localhost:8080/xxx/updateItem?id=1&name=iphone&price=1000
```

那么POJO类形式如下
```java
public class Items{
    private Integer id;
    private String name;
    private String price;
}
```

controller方法,url请求参数会自动传入到item中
```java
@RequestMapping("/updateItem")
public String updateItem(Integer id,Item item) {
}
```
## 绑定包装POJO类

所谓的包装POJO类，指的就是POJO中包含另一个POJO的类

如
```java
public class Zoo{
    private Animals animals;
}

public class Animals{
    private String name;
}
```

在前端页面
```html
<tr>
    <td>动物名称<input type="text" name="animals.name"></td>
    <td><input type="submit" value="查询"></td>
    <td></td>
</tr>
```

控制器,zoo对象中，animals.name已经被赋值
```java
public String queryAnimals(Zoo zoo){
   zoo.getAnimals.getName();
}
```

## 使用简单类型数组接收参数

使用要求：  
通过HTTP请求批量传递简单类型数据的情况，Controller方法中可以用String[]或者pojo的String[]属性接收（两种方式任选其一），但是不能使用集合接收。

如,url传递了id的三个值(1个Key,对应3个值)
```
http://localhost:8080/xxx/deleteItem?id=1&id=2&id=3
```

controller方法,使用Integer[] 接收
```java
@RequestMapping("/deleteItem")
	public String deleteitem(Integer[] ids){
		
		return "success";
	}
```

## 使用POJO类型集合或者数组接收参数

使用要求  
批量传递的请求参数，最终要使用List<POJO>来接收，那么这个List<POJO>必须放在另一个POJO类中。


如，url
```
http://localhost:8080/xxx/batchUpdateItem?
itemList[0].id=1& itemList[0].name=iphone& itemList[0].price=1000&itemList[1].id=2& itemList[1].name=iphone x&itemList[1].price=2000

```
POJO类如下,包含了List< Item >集合
```java
public class ItemQueryVO {

	private List<Item> itemList;
}
```

控制器如下,方法参数时一个POJO类（内含List< Item >）
```java
	@RequestMapping("/batchUpdateItem")
	public String batchUpdateItem(ItemQueryVO vo) {
	}
```

## 自定义参数绑定

URL
```
http://localhost:8080/xxx/saveItem?date=2018-08-12
```

controller方法,我们想要Date参数类型来接收请求参数
```java
@RequestMapping("/saveItem")
public String saveItem(Date date){
    System.out.println("接收到的请求参数是："+ date);
    return "success";
}
```

然后这样会报错,解决办法是使用自定义的Converter

首先自定义一个DateConverter并实现Converter接口，重写convert方法,在方法中进行String->Date逻辑运算
```java
public class DateConverter implements Converter<String, Date> {

	@Override
	public Date convert(String source) {
		SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
		try {
			return simpleDateFormat.parse(source);
		} catch (ParseException e) {
			e.printStackTrace();
		}
		return null;
	}
}
```

其次，需要在springmvc.xml中将其配置
```xml
<bean id="conversionService"
    class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
    <property name="converters">
        <set>
            <bean class="com.kkb.ssm.controller.converter.DateConverter"/>
        </set>
    </property>
</bean>
```
配置完成后，当SpingMVC遇到String要转Date，就会自动去寻找DateConverter来进行转换