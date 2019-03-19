#  注册Handler（处理器）


处理器开发方式有多种：实现HttpRequestHandler接口、实现Controller接口、注解方式等。
不过企业开发中，推荐使用注解方式开发处理器

注解方式必要的注解主要有以下两个
1. @Controller注解：在类上添加该注解，指定该类为一个请求处理器，不需要实现任何接口或者继承任何类。
2. @RequestMapping注解：在方法上或者类上添加该注解，指定请求的url由该方法处理。如果url-pattern配置的是*的话，url中的“”可以加也可以不加
3. (注解方式别忘记打开组件扫描)

处理器的返回值是ModelAndView对象，关于该对象，主要有两个功能
1.  将数据存储到Request域中
2. 设置响应视图，比如将视图设置为“/WEB-INF/jsp/item-list.jsp”

示例代码：
```java
@Controller
public class ItemController {

	@RequestMapping("/queryItem")
	public ModelAndView queryItem() throws Exception {
		
		List<Item> itemList = new ArrayList<>();
		
		//商品列表
		Item item_1 = new Item();
		item_1.setName("联想笔记本");
		item_1.setPrice(6000f);
		item_1.setDetail("ThinkPad T430 联想笔记本电脑！");
		
		Item item_2 = new Item();
		item_2.setName("苹果手机");
		item_2.setPrice(5000f);
		item_2.setDetail("iphone6苹果手机！");
		
		itemList.add(item_1);
		itemList.add(item_2);
		//创建modelandView对象
		ModelAndView modelAndView = new ModelAndView();
		//添加model
		modelAndView.addObject("itemList", itemList);
		//添加视图
		modelAndView.setViewName("/WEB-INF/jsp/itemList.jsp");
		return modelAndView;
	}

}
```