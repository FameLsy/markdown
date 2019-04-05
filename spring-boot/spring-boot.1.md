<!-- 工程创建 -->

[官网创建地址](https://start.spring.io/)

![spring-boot1](https://raw.githubusercontent.com/FameLsy/Images/master/spring-boot/spring-boot1.png)

# 工程创建

在官网创建完工程后，导入到IDE中,**Spring-Boot**项目自动生成了一个**Application**启动类；在启动类同包下创建子包，在其中编写SpringMVC的处理器类（必须在子孙包中，同级包不扫描）；运行工程，只需要运行启动类即可。

运行信息如下
```
2019-04-05 11:16:40.683  INFO 6276 --- [           main] c.l.s.SpringBootDemoApplication          : Starting SpringBootDemoApplication on DESKTOP-B804V28 with PID 6276 (C:\Users\Liisyu\Desktop\spring-boot-demo\target\classes started by Liisyu in C:\Users\Liisyu\Desktop\spring-boot-demo)
2019-04-05 11:16:40.686  INFO 6276 --- [           main] c.l.s.SpringBootDemoApplication          : No active profile set, falling back to default profiles: default
2019-04-05 11:16:41.820  INFO 6276 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2019-04-05 11:16:41.847  INFO 6276 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2019-04-05 11:16:41.847  INFO 6276 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.17]
2019-04-05 11:16:41.946  INFO 6276 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2019-04-05 11:16:41.946  INFO 6276 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1213 ms
2019-04-05 11:16:42.124  INFO 6276 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2019-04-05 11:16:42.312  INFO 6276 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2019-04-05 11:16:42.315  INFO 6276 --- [           main] c.l.s.SpringBootDemoApplication          : Started SpringBootDemoApplication in 1.932 seconds (JVM running for 2.391)
```

可以看到
- Tomcat启动，端口号为8080
- SpringMVC的中央调度器映射请求，即url-pattern值为/
- 项目的context path 为空

对于Spring-Boot打包的jar包，是可执行文件,通过以下命令来运行它
```
java -jar xxx.jar
```

# 工程解析

## 工程启动类

Spring-Boot会自动创建如下的启动类
```java

@SpringBootApplication
public class SpringBootDemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringBootDemoApplication.class, args);
	}

}
```

进入**@SpringBootApplication**注解
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration //Spring-Boot配置
@EnableAutoConfiguration //该注解表明Spring-Boot会进行自动配置
```

进入 **@SpringBootConfiguration**,不难发现，其本质就是一个 **@Configuration**

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
}
```
## resources目录

目录结构如下
- static：用来放置静态资源,如css、js
- templates: 用来放置动态资源，如jsp(不建议)，Thymeleaf(建议)
- application.properties：Spring-Boot核心配置文件

![spring-boot2](https://raw.githubusercontent.com/FameLsy/Images/master/spring-boot/spring-boot2.png)

# Spring-Boot依赖

打开POM.XML，Spring-Boot只有两个依赖
- Web启动依赖
- 测试启动依赖
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

此外，它还依赖于父工程
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.4.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

进入父工程,如下是一个执行目标为repackage的插件，通过该插件，在原先Maven的packge插件打包(普通jar包)之后，再一次进行打包，使其变为可执行的jar文件；同时，原来的jar包扩展名变为.oriniaml
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <executions>
        <execution>
            <id>repackage</id>
            <goals>
                <goal>repackage</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <mainClass>${start-class}</mainClass>
    </configuration>
</plugin>
```

同时，该工程依旧有父工程
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.1.4.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
```

而该工程，定义了各种依赖的版本
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot</artifactId>
            <version>2.1.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-test</artifactId>
            <version>2.1.4.RELEASE</version>
        </dependency>
...
```

## 修改依赖

只需在当前项目的pom.xml文件使用新版本好覆盖原版本号即可


# 可执行的jar文件

经过repackage后，jar包添加啦Spring Boot的各种依赖
- BOOF-INF: 存放当前应用程序的类及其所依赖的类
- META-INF:存放Maven相关配置文件
- org: 存放SprignBoot启动所需类

![spring-boot3](https://raw.githubusercontent.com/FameLsy/Images/master/spring-boot/spring-boot3.png)

# mvnw

Spring-Boot创建时自带了一个文件及和两个文件
- .mvn文件夹
- mvnw和mvnw.cmd文件

作用：Maven版本和插件的配合存在一定的问题，有时候为了让插件正常运行，可能需要切换到老版本的Maven，而切换的话需要重新安装一个Maven，过于麻烦。而**Maven Wrapper**是由第三方提供的工具，可以轻松完成切换Maven版本的工作，SpringBoot中的mvnw即为安装好的**Maven Wrapper**，mvnw.cmd为**Maven Wrapper**的命令运行窗口，mvnw为**Maven Wrapper**的配置文件

文件可以删除，如果需要使用，自行参阅Maven Wrapper官网文档

# war 包的创建

创建war包与jar包类似，不同在于工程目录下多出了**ServletInitializer**类,该类重写了父类的**configure**方法，该方法完成了所有应用程序的默认配置
```java
public class ServletInitializer extends SpringBootServletInitializer {

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
		return application.sources(SpringBootWarApplication.class);
	}

}

```

此外，在pom.xml下
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <!-- 因为war包不需要打包ServletAPI,所以设置了provided -->
    <scope>provided</scope> 
</dependency>
```


# Actuator

Actuator是SpringBoot提供的对应用系统的自省和监控的集成功能，可以对应用系统进行配置查看，相关功能统计等。

## 基本环境的搭建

任何一个Spring Boot工程都可以使用Actuator进行监控，首先需要导入依赖

```xml
<!-- 版本号由父工程提供 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
    <scope>test</scope>
</dependency>
```

修改配置文件
```xml
# 修改内置tomcat
server.port=8184
server.servlet.context-path=/first

# Actuator监控端口号与上下文路径
management.server.port=9999
management.server.servlet.context-path=/ac
#指定监控中断的基本路径，默认actuator
management.endpoints.web.base-path=/base
```

访问测试
```
localhost:9999/ac/base/health
<!-- 输出 -->
{"status":"UP"}
```

## 添加Info信息

Info信息会以Json格式显示
```

# 自定义
info.liisyu.name=liisyu
info.liisyu.url=www.liisyu.com
info.liisyu.addr=tongxiang

# 从pom.xml中读取
info.project.groupid=@project.groupId@
info.project.artifactid=@project.artifactId@
info.project.version=@project.version@
info.project.name=@project.name@
```

访问测试
```
http://localhost:9999/ac/base/info

# 输出 
{"liisyu":{"name":"liisyu","url":"www.liisyu.com","addr":"tongxiang"},"project":{"groupid":"com.liisyu","artifactid":"spring-boot-demo","version":"0.0.1-SNAPSHOT","name":"spring-boot-demo"}}
```

## 开放其他监控终端

默认情况下，Actuator仅开放了health与Info两个终端

```java
# 开放所有终端
management.endpoints.web.exposure.include=*
```

mappings终端：可以查看当前工程中所有的URI与处理器映射关系，一级详细的处理器方法及其映射规则

beans终端：查看当前应用程序的bean

env终端：查看当前应用程序运行主机的所有软硬件环境信息

# 单独关闭某些监控终端

```
management.endpoints.web.exposure.exclude=env,beans
```

# 常用的监控终端

|HTTP方法	|路径|	描述	|鉴权|
|--|--|--|--|
|GET|	/autoconfig|	查看自动配置的使用情况|	true|
|GET|	/configprops|	查看配置属性，包括默认配置|	true|
|GET|	/beans|	查看bean及其关系列表	|true|
|GET|	/dump	|打印线程栈	|true|
|GET|	/env	|查看所有环境变量|	true|
|GET|	/env/{name}	|查看具体变量值	|true|
|GET|	/health	|查看应用健康指标	|false|
|GET|	/info	|查看应用信息	|false|
|GET|	/mappings|	查看所有url映射	|true|
|GET|	/metrics|	查看应用基本指标|	true|
|GET|	/metrics/{name}|	查看具体指标|	true|
|POST|	/shutdown	|关闭应用	|true|
|GET|	/trace	|查看基本追踪信息	|true|

# yml配置方式

yml配置方式是多级配置
```
server:
  port: 8181
```
