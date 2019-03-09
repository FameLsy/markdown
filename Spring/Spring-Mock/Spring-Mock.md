---
title: SpringMVC-Mock
tgas: 
- SpringMVC-Mock
categories: 
- SpringMVC
---

# 简介

Mock测试：
1. 在测试过程中，对于某些不容易构造或者不容易获取的对象，用一个虚拟的对象来创建以便测试的测试方法，就是mock测试
2. 基于RESTful风格的SpringMVC的测试，我们可以测试完整的Spring MVC流程，即从URL请求到控制器处理，再到视图渲染都可以测试。

# 依赖
```xml
<!-- spring 单元测试组件包 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>5.0.7.RELEASE</version>
		</dependency>

		<!-- 单元测试Junit -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
		</dependency>

		<!-- Mock测试使用的json-path依赖 -->
		<dependency>
			<groupId>com.jayway.jsonpath</groupId>
			<artifactId>json-path</artifactId>
			<version>2.2.0</version>
		</dependency>
```

# Mock相关类与方法

MockMVCBuilder
1. MockMvcBuilder是用来构造MockMvc的构造器
2. 其主要有两个实现：StandaloneMockMvcBuilder和DefaultMockMvcBuilder，分别对应之前的两种测试方式。
3. 直接使用静态工厂MockMvcBuilders创建即可

MockMVCBuilders
1. 负责创建MockMVCBuilder对象
2. 有两种创建方式  
    第一种：standaloneSetup(Object... controllers):通过参数指定一组控制器，不需要从上下文  
    第二种：webAppContextSetup(WebApplicationContext wac)：指定WebApplicationContext，将会从该上下文获取相应的控制器并得到相应的MockMvc  

MockMvc
1. 对于服务器端的Spring MVC测试支持主入口点。
2. 通过MockMVCBuilder构造
3. 核心方法：perform(RequestBuilder rb)->执行一个RequestBuilder请求，会自动执行SpringMVC的流程并映射到相应的控制器执行处理，该方法的返回值是一个ResultActions；

ResultActions
1. andExpect：添加ResultMatcher验证规则，验证控制器执行完成后结果是否正确；
2. andDo：添加ResultHandler结果处理器，比如调试时打印结果到控制台；
3. andReturn：最后返回相应的MvcResult；然后进行自定义验证/进行下一步的异步处理；

MockMvcRequestBuilders
1. 用来构建请求的
2. 其主要有两个子类MockHttpServletRequestBuilder和MockMultipartHttpServletRequestBuilder（如文件上传使用），即用来Mock客户端请求需要的所有数据。

MockMvcResultMatchers
1. 用来匹配执行完请求后的结果验证
2. 如果匹配失败将抛出相应的异常
3. 包含了很多验证API方法

MockMvcResultHandlers
1. 结果处理器，表示要对结果做点什么事情
2. 比如此处使用MockMvcResultHandlers.print()输出整个响应结果信息

MvcResult
1. 单元测试执行结果，可以针对执行结果进行自定义验证逻辑

# 使用

步骤：
1. 使用@WebAppConfiguration注解：可以在单元测试的时候，不用启动Servlet容器，就可以获取一个Web应用上下文
2. 自动注入一个WebApplicationContext
3. 获取一个mockMvc实例
4. 通过mockMvc进行相应的操作

```java
//

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring/*.xml")
@WebAppConfiguration
public class TestMockMVC {

	@Autowired
	private WebApplicationContext wac;

	private MockMvc mockMvc;

	@Before
	public void setup() {
		// 初始化一个MockMVC对象的方式有两种：单独设置、web应用上下文设置
		// 建议使用Web应用上下文设置
		mockMvc = MockMvcBuilders.webAppContextSetup(wac).build();
	}

	@Test
	public void test() throws Exception {
		// 通过perform去发送一个HTTP请求
		// andExpect：通过该方法，判断请求执行是否成功
		// andDo :对请求之后的结果进行输出
		MvcResult result = mockMvc.perform(MockMvcRequestBuilders.get("/item/showEdit").param("id", "1"))
				.andExpect(MockMvcResultMatchers.view().name("item/item-edit"))
				.andExpect(MockMvcResultMatchers.status().isOk())
				.andDo(MockMvcResultHandlers.print())
				.andReturn();
		
		System.out.println("================================");
		System.out.println(result.getHandler());
	}
	
	@Test
	public void test2() throws Exception {
		// 通过perform去发送一个HTTP请求
		// andExpect：通过该方法，判断请求执行是否成功
		// andDo :对请求之后的结果进行输出
		MvcResult result = mockMvc.perform(get("/item/findItem").param("id", "1").accept(MediaType.APPLICATION_JSON))
				.andExpect(status().isOk())
				.andExpect(content().contentTypeCompatibleWith(MediaType.APPLICATION_JSON))
				.andExpect(jsonPath("$.id").value(1))
				.andExpect(jsonPath("$.name").value("台式机123"))
				.andDo(print())
				.andReturn();
		
		System.out.println("================================");
		System.out.println(result.getHandler());
	}
}
```