---
title: Servlet-过滤器-接口
tags: 
- Servlet-过滤器-接口
categories: 
- Servlet
---

# Filter接口

Filter接口：javax.serlvet.Filter

Filter接口的三个方法
1. init()
2. doFilter()
3. destryo()

```java
public interface Filter {

    /**
    *web程序启动时调用，用于初始化Filter
    *@param filterConfig 从该参数获取初始化信息，ServletContext
    */
    public void init(FilterConfig filterConfig) throws ServletException;
	/**
    *过滤器具体处理方法
    *@param chain 滤镜链，通过chain.doFilter(request, response)将请求传给下一个Filter或Servlet
    */
    public void doFilter(ServletRequest request, ServletResponse response,
                         FilterChain chain)
            throws IOException, ServletException;

    public void destroy();
}
```

>注:  
>chain.doFilter(request, response)会将请求传递给chain中的下一个Filter直到所有Filter走完，则交给Servlet处理