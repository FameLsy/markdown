---
title: SpringMVC-原理分析
date: 2018-02-28 00:00:01
tgas: 
- Spring-原理分析
categories: 
- SpringMVC
---

# 原理分析

## HandlerMapping是如何初始化的?

查看DispatcherServlet,其提供了初始化的具体方法
```java
private void initHandlerMappings(ApplicationContext context) {...}
```
先不管如何实现,追踪下去,由如下调用链
```
initHandlerMappings()
->initStrategies()
->onRefresh()
->父类FrameworkServlet.onRefresh()
->initWebApplicationContext()
->initServletBean()
->父类HttpServletBean.initServletBean()
->init();
```
HttpServietBean是继承于HttpServlet,也就是说,容器会在实例化DispatcherServlet,会先调用该类的init,然后执行下去,最终执行initHandlerMappings(),得到HandlerMapping的一个实例

此外,一开始初始化DispatcherServlet时,会加载默认的配置,默认文件位于spring-webmvc包下的/org/springframework/web/servlet/DispatcherServlet.properties,默认加载了一些springmvc默认的其他组件

如下是三大组件的默认配置
```
<!-- 映射器 -->
org.springframework.web.servlet.HandlerMapping=org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping,\
	org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping

!-- 处理适配器 -->
org.springframework.web.servlet.HandlerAdapter=org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter,\
	org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter,\
	org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter

<!-- 视图解析器 -->
org.springframework.web.servlet.ViewResolver=org.springframework.web.servlet.view.InternalResourceViewResolver
```

## Handler获取

DispatcherServlet会根据URL寻找对应的处理器(Handler),那么它是如何寻找?

首先是SpringMVC的入口,即DispatcherServlet,而servlet类,入口方法为doService();

在这里我一共分了三部分
1. part one:这里是一些日志的代码
2. part two :根据注释,是一些关于快照的操作
3. part three:根据注释:使框架对象可供处理程序和视图对象使用(很明显,这里是核心部分)

```java
@Override
protected void doService(HttpServletRequest request, HttpServletResponse response) throws Exception {
	//part one
	if (logger.isDebugEnabled()) {
		String resumed = WebAsyncUtils.getAsyncManager(request).hasConcurrentResult() ? " resumed" : "";
		logger.debug("DispatcherServlet with name '" + getServletName() + "'" + resumed +
				" processing " + request.getMethod() + " request for [" + getRequestUri(request) + "]");
	}

	//part two
	// Keep a snapshot of the request attributes in case of an include,
	// to be able to restore the original attributes after the include.
	Map<String, Object> attributesSnapshot = null;
	if (WebUtils.isIncludeRequest(request)) {
		attributesSnapshot = new HashMap<>();
		Enumeration<?> attrNames = request.getAttributeNames();
		while (attrNames.hasMoreElements()) {
			String attrName = (String) attrNames.nextElement();
			if (this.cleanupAfterInclude || attrName.startsWith(DEFAULT_STRATEGIES_PREFIX)) {
				attributesSnapshot.put(attrName, request.getAttribute(attrName));
			}
		}
	}

    // part three
	// Make framework objects available to handlers and view objects.
	request.setAttribute(WEB_APPLICATION_CONTEXT_ATTRIBUTE, getWebApplicationContext());
	request.setAttribute(LOCALE_RESOLVER_ATTRIBUTE, this.localeResolver);
	request.setAttribute(THEME_RESOLVER_ATTRIBUTE, this.themeResolver);
	request.setAttribute(THEME_SOURCE_ATTRIBUTE, getThemeSource());

	if (this.flashMapManager != null) {
		FlashMap inputFlashMap = this.flashMapManager.retrieveAndUpdate(request, response);
		if (inputFlashMap != null) {
			request.setAttribute(INPUT_FLASH_MAP_ATTRIBUTE, Collections.unmodifiableMap(inputFlashMap));
		}
		request.setAttribute(OUTPUT_FLASH_MAP_ATTRIBUTE, new FlashMap());
		request.setAttribute(FLASH_MAP_MANAGER_ATTRIBUTE, this.flashMapManager);
	}

	try {
		doDispatch(request, response);
	}
	finally {
		if (!WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
			// Restore the original attribute snapshot, in case of an include.
			if (attributesSnapshot != null) {
				restoreAttributesAfterInclude(request, attributesSnapshot);
			}
		}
	}
}
```

可以看出,开始是request的一些赋值设定,真正的核心就是try中的语句
```java
doDispatch(request, response);
```

进入doDispatch(),通过方法注释,确定这里就是将请求调度到Handler的地方,方法体内,它也明确标识了出获取Handler的部分,即通过getHandler方法

```java
// Determine handler for the current request.
mappedHandler = getHandler(processedRequest);
```

进入getHandler()方法,先看注释,该方法返回的是一个HandlerExecutionChain,(这个类内部,包含了Handler和一些其他的),同时,进行匹配的事,通过HandlerMapping来实现了
```java
	/**
	 * Return the HandlerExecutionChain for this request.
	 * <p>Tries all handler mappings in order.
	 * @param request current HTTP request
	 * @return the HandlerExecutionChain, or {@code null} if no handler could be found
	 */
	@Nullable
	protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
		if (this.handlerMappings != null) {
			for (HandlerMapping hm : this.handlerMappings) {
				if (logger.isTraceEnabled()) {
					logger.trace(
							"Testing handler map [" + hm + "] in DispatcherServlet with name '" + getServletName() + "'");
				}
				HandlerExecutionChain handler = hm.getHandler(request);
				if (handler != null) {
					return handler;
				}
			}
		}
		return null;
	}
```

至此,对于DispatcherServlet会根据URL寻找对应的处理器(Handler),已经解决

