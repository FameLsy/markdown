---
title: SpringMVC-JSON数据交互
tgas: 
- SpringMVC-JSON数据交互
categories: 
- SpringMVC
---


# 依赖

```xml
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-databind</artifactId>
	<version>2.9.6</version>
</dependency>
```

# JSON交互方式

JSON的交互方式有两种
1. 请求是KV，响应是JSON（推荐使用）
2. 请求是JSON，响应是JSON

## 请求是KV，响应是JSON（推荐使用）

controller如下:  
@ResponseBody 可以将返回值转成json串响应给前台
```java
// 输入是key/value，输出是json
// @ResponseBody 将返回值转成json串响应给前台
@RequestMapping("/responseKV")
@ResponseBody
public Item responseKV(Item item) {
return item;
}
```

## 请求是JSON，响应是JSON

controller如下:
1. @ResponseBody 可以将返回值转成json串响应给前台
2. @RequestBody 将请求的json串转成java对象
```java
@Controller
public class JsonController {

	// 输入是json，输出是json
	@RequestMapping("/requestJson")
    @ResponseBody
	public Item requestJson(@RequestBody Item item) {

		return item;
	}
}
```