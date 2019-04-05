<!-- 单元测试 -->

# 导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

# 测试类

测试类需要两个注解
```
@RunWith(SpringRunner.class)
@SpringBootTest(classes=SpringboottestApplication.class)
