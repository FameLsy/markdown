<!-- 多环境选择 -->
# 什么是多环境

在开发过程中，应用可能处于开发、测试、生产环境等。不同的环境，运行接口的实现类可能也不同；对于不同的环境，需要使用不同的配置文件，执行不同的类。

# 多环境选择

步骤：
- 定义多个子配置文件ww
- 在主配置文件中做环境选择
- 在接口实现类上通过@Profile做环境选择

首先，创建一个工程，然后在**src/main/resources**中定义子配置文件
```xml
# 配置文件名需满足：application-{profile}.properties

# 开发环境
application-dev.properties
# 生产环境
application-prod.properties
# 测试环境
application-testproperties
```

为不同的环境设置不同的接口和上下文路径
```
# application-dev.properties
server.port=8888
server.servlet.context-path=/dev

# application-prod.properties
server.port=7777
server.servlet.context-path=/prod
```

在主配置文件中激活
```
#application.properties
# spring.profiles.active的值，即位{profile}的值，通过该属性可以选择加载配置文件
spring.profiles.active=dev
```

此外，在Spring bean代码上，通过@Profile来指定bean在某个环境下创建，如
```java
//生产环境下的业务接口实现类
@Service
@Profile("prod")
public class ProduceSerciceImpl implements ISomeService(){
    ....
}
```

在命令行下运行，原理是命令行属性值优先级高于配置文件
```
//通过传入spring.profiles.active来选择环境
java -jar xx.jar --spring.profiles.active=prod
```

# 多环境选择二：单配置实现方式

该方式只能使用application.yml文件

```
# 选择激活
spring:
  profiles:
    active:
      - prod

# 使用三个-分隔开
--- 
spring:
  profiles: dev

server:
  port: 8888
  servlet:
    context-path: /dev

---

spring:
  profiles: prod

server:
  port: 9999
  servlet:
    context-path: /prod
```