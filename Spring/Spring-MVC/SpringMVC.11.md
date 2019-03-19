<!-- ---
title: SpringMVC-@ControllerAdvice
tgas: 
- SpringMVC-@ControllerAdvice
categories: 
- SpringMVC
--- -->

@ControllerAdvice：
1. 该注解了@Controller注解的类进行增强。
2. 该注解使用@Component注解，这样的话当我们使用< context:component-scan >扫描时也能扫描到。
3. 该注解内部使用@ExceptionHandler、@InitBinder、@ModelAttribute注解的方法会应用到所有的Controller类中 @RequestMapping注解的方法。

# 使用

该注解的使用步骤为
1. 通过@ControllerAdvice注解，配置一个自定义的ControllerAdvice
2. 在内部使用@ModelAttribute、@InitBinder、	@ExceptionHandler(Exception.class)来实现对@Controller的增强

```java
@ControllerAdvice
public class MyControllerAdvice {

	//应用到所有@RequestMapping注解方法，在其执行之前把返回值放入ModelMap中
	@ModelAttribute
	public Map<String, Object> ma(){
		Map<String, Object> map = new HashMap<>();
		map.put("name", "tom");
		return map;
	}
	
	//应用到所有【带参数】的@RequestMapping注解方法，在其执行之前初始化数据绑定器
	@InitBinder
	public void initBinder(WebDataBinder dataBinder) {
	}
	
	//应用到所有@RequestMapping注解的方法，在其抛出指定异常时执行
	@ExceptionHandler(Exception.class)
	@ResponseBody
	public String handleException(Exception e) {
		return e.getMessage();
	}
}
```

# 内置注解解释

## @ModelAttribute

@ModelAttribute:
1. 该注解主要作用于ModelMap这个模型对象的，用于在视图中显示数据。
2.  该注解注意事项：和@ResponseBody注解的使用是互斥的。

注解有三个用途
1. 作用于非@RequestMapping注解方法  
    该方法会在在本类内所有@RequestMapping注解方法之前执行  
    如果方法有返回值，则直接将该返回值放入ModelMap中，key可以指定；如果方法没有返回值，则可以利用它的执行时机这一特点，做一些预处理。
2. 作用于有@RequestMapping注解方法  
    返回值会放入ModelMap中
    逻辑视图名称根据请求URL生成，比如URL：item/itemList，那么这个URL就是逻辑视图名称
3. 作用于方法参数（有@RequestMapping注解的方法)  
    第一步，它会获取ModelMap中指定的数据（由@ModelAttribute注解的方法产生）  
    第二步：将使用该注解的参数对象放到ModelMap中。  
    如果两步中的对象在ModelMap中key相同，则合并这两部分对象的数据。

## @InitBinder

 @InitBinder:
1. 由 @InitBinder 标识的方法，可以通过PropertyEditor解决数据类型转换问题，比如String-->Date类型
2. PropertyEditor只能完成String-->任意类型的转换
3. 它可以对 WebDataBinder 对象进行初始化，WebDataBinder 是 DataBinder 的子类,用于完成由表单字段到 JavaBean 属性的绑定。

>注意事项：  
> @InitBinder方法不能有返回值,它必须声明为void。  
>@InitBinder方法的参数通常是 WebDataBinder。

## @ExceptionHandler

@ExceptionHandler
1. 表示Controller中任何一个@RequestMapping方法发生异常，则会被注解了@ExceptionHandler的方法拦截到。
2. 拦截到对应的异常类则执行对应的方法，如果都没有匹配到异常类，则采用近亲匹配的方式。
