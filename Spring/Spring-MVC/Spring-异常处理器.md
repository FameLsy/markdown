---
title: SpringMVC-异常处理器
tgas: 
- SpringMVC-异常处理器
categories: 
- SpringMVC
---

springmvc在处理请求过程中出现异常信息交由异常处理器进行处理，自定义异常处理器可以实现一个系统的异常处理逻辑。

# 异常处理思路
系统的dao、service、controller出现都通过throws Exception向上抛出，最后由springmvc前端控制器交由异常处理器进行异常处理，如下图：

![异常](https://raw.githubusercontent.com/FameLsy/Images/master/spring/异常.png)

>注意  
>全局范围只有一个异常处理器。

# 异常处理器的使用

需要的准备工作为
1. 自定义一个异常类
2. 自定义异常处理器(实现HandlerExceptionResolver)
3. 配置异常处理器

自定义异常类
```java
public class MyExcepetion extends Exception {
	
	private static final long serialVersionUID = 1L;

	//异常信息
	private String message;
	
	public MyExcepetion(String message) {
		super(message);
		this.message = message;
	}

	public String getMessage() {
		return message;
	}

	public void setMessage(String message) {
		this.message = message;
	}
}
```

自定义异常处理器
1. 实现HandlerExceptionResolver
2. 重写resolveException()
```java
public class MyExcepetionResolver implements HandlerExceptionResolver {

	@Override
	public ModelAndView resolveException(HttpServletRequest request,
			HttpServletResponse response, Object handler, Exception ex) {
		
		//自定义预期异常
		MyExcepetion myExcepetion = null; 
		//如果抛出的是系统自定义的异常
		if(ex instanceof MyExcepetion){
			myExcepetion = (MyExcepetion) ex;
		}else{
			myExcepetion = new MyExcepetion("未知错误");
		}
		
		ModelAndView modelAndView = new ModelAndView();
		//把错误信息传递到页面
		modelAndView.addObject("message", myExcepetion.getMessage());
		//指向错误页面
		modelAndView.setViewName("error");
		return modelAndView;
	}
}
```

配置异常处理器
```
<!-- springmvc.xml -->
<bean class="com.kkb.ssm.resolver.BusinessExceptionResolver"/>
```