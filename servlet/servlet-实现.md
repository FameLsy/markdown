---
title: Servlet-实现
tags: 
- Servlet-实现
categories: 
- Servlet
---

# Servlet-实现

想要实现Servlet,只需要继承HttpServlet,主要重写三个方法
1. doGet()
2. doPost()
3. getLastModified()

```java
/**
 * @author liisyu
 * @date 2019/3/5
 */
public class servlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }

    @Override
    protected long getLastModified(HttpServletRequest req) {
        return super.getLastModified(req);
    }
    
}
```

doGet()：
1. 以GET方式访问页面时执行该函数
2. 执行doGet前会先 执行 getLastModified()  
    如果返回的值与上次访问返回的数值相同，则认为该文档没有更新，浏览器采用缓存而不执行doGet()
    如果返回-1，则认为是实时更新，总是执行doGet()

goPost():
1. 以POST方式访问页面时执行该函数
2. 执行doPost 不会 执行getLastModified()

getLastModified():
1. 返回该Servlet生成的文档的更新时间，对Get方式访问有效
2. 返回的时间为相对于1970.01.01 08:00:00的毫秒数
3. 返回值是-1则认为是实时更新，默认-1


