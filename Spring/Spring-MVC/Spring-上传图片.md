---
title: SpringMVC-上传图片
tgas: 
- SpringMVC-上传图片
categories: 
- SpringMVC
---

SpringMVC文件上传的实现，是由commons-fileupload这个jar包实现的。

# 依赖
```xml
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.3.1</version>
</dependency>
```

# 使用

配置multipart类型解析器
```xml
<!-- springmvc.xml -->
<!-- multipart类型解析器，文件上传 -->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	<!-- 上传文件的最大尺寸 5M-->
	<property name="maxUploadSize" value="5242880"/>
</bean>
```

controller
```java
@RequestMapping(value = "/updateItem")
public String updateItem(Model model,Item item,
				MultipartFile pictureFile) throws Exception {
		
	if(pictureFile != null){
		System.out.println(pictureFile.getOriginalFilename());
		//原始图片名称
		String originalFilename = pictureFile.getOriginalFilename();
		//如果没有图片名称，则上传不成功
		if(originalFilename != null && originalFilename.length()>0)
		{
			//存放图片的物理路径
			String picPath = "E:\\03-teach\\07-upload\\temp\\";
			//新文件的名称
			String newFileName = UUID.randomUUID()+originalFilename.substring(originalFilename.lastIndexOf("."));
			//新的文件
			File newFile = new File(picPath+newFileName);
			//把上传的文件保存成一个新的文件
			pictureFile.transferTo(newFile);
			//同时需要把新的文件名更新到数据库中
			item.setPic(newFileName);
		}else{
			throw new BusinessException("图片名称不存在，上传不成功");
		}
	}
		
	// 根据页面传入的商品信息，调用修改方法，进行修改（此时还没有讲参数绑定，暂时无法进行）
	itemService.updateItem(item);
	return "success";
}
```

# 配置文件的虚拟目录

在图片虚拟目录中，一定将图片目录分级创建（提高i/o性能），一般我们采用按日期(年、月、日)进行分级创建

在tomcat安装目录中的conf/server.xml文件中，添加虚拟目录：
<Context docBase="E:\03-teach\07-upload\temp" path="/pic" reloadable="false"/>
