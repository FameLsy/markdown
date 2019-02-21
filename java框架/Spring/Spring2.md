
### 2.2.5 Environment
源码如下
```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package org.springframework.core.env;

public interface Environment extends PropertyResolver {
//获取激活的profile 名称
    String[] getActiveProfiles();
//获取默认的profile名称

    String[] getDefaultProfiles();

//如果environment支持传入的profile名称（只要一个满足就可以），就返回true
//用来判断profile的激活状态
    boolean acceptsProfiles(String... var1);
}
发现其继承了PropertyResolver,源码如下

//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package org.springframework.core.env;

public interface PropertyResolver {
//检测属性是否存在
    boolean containsProperty(String var1);

//获取属性
    String getProperty(String var1);

//获取属性，若属性不存在，使用默认值var2
    String getProperty(String var1, String var2);

//获取属性，并将其转成T类型
    <T> T getProperty(String var1, Class<T> var2);

//获取属性，并将其转成T类型，并可以设置一个T类型的默认值
    <T> T getProperty(String var1, Class<T> var2, T var3);

//讲属性解析为类
    /** @deprecated */
    @Deprecated
    <T> Class<T> getPropertyAsClass(String var1, Class<T> var2);

//获取属性，属性必须存在，不存在抛出异常
    String getRequiredProperty(String var1) throws IllegalStateException;

//获取属性，属性必须存在，并转成T类型，不存在抛出异常
    <T> T getRequiredProperty(String var1, Class<T> var2) throws IllegalStateException;

//
    String resolvePlaceholders(String var1);

    String resolveRequiredPlaceholders(String var1) throws IllegalArgumentException;
}
```

## 2.4 bean的作用域
作用域类型
- 单例（Singleton）：在整个应用中，只创建bean的一个实例。
- 原型（Prototype）：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例。
- 会话（Session）：在Web应用中，为每个会话创建一个bean实例。
- 请求（Rquest）：在Web应用中，为每个请求创建一个bean实例

注解方式：
- @scope注解：选择其他作用域，与@Component和@Bean一起使用。
- @Scope(Value=ConfigurableBeanFactory.SCOPE_PROTOTYPE):定义为原型作用域。
- @Scope(Value=ConfigurableBeanFactory.SCOPE_SINGLETON)：定义为单例作用域。
- @Scope(Value=WebApplicationContext.SCOPE_REQUEST):定义为请求作用域
- @Scope(Value=WebApplicationContext.SCOPE_SESSION)：定义为会话作用域

##  2.5 运行注入
到目前为止，注入的都是bean对象
但一个bean，往往还有其他的值，如String、int等，最简单的就是直接再构造方法上传值，但这样值就固定为name和titile了
```java
    //为CDPlayer创建bean
    @Bean
    public MediaPlayer mediaPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc,"name", "title");
    }
```
如果想要再运行时，再决定值，可以使用注入外部值的方法，也可以使用spring提供的两种方法【属性占位符、SpEL表达式】
###  2.5.1 注入外部值
示例代码
接口1
```java
package com.spring.chapter3.RuntimeValueInjection;

/**
 * 一个光盘接口
 */
public interface CompactDisc {
    void sing();//光盘的sing功能
}
```
接口1实现
```java
package com.spring.chapter3.RuntimeValueInjection;

import org.springframework.stereotype.Component;

/**
 * CompactDisc实现类
 */
public class SgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("sgtPeppers的sing方法");
    }
}
```
接口2
```java
package com.spring.chapter3.RuntimeValueInjection;

/**
 * 一个光盘接口
 */
public interface CompactDisc {
    void sing();//光盘的sing功能
}
```
接口2实现
```java
package com.spring.chapter3.RuntimeValueInjection;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;
    private String name;
    private String title;

    public CDPlayer(CompactDisc compactDisc, String name, String title) {
        this.compactDisc = compactDisc;
        this.name = name;
        this.title = title;
    }

    @Override
    public String toString() {
        return "CDPlayer{" +
                "compactDisc=" + compactDisc +
                ", name='" + name + '\'' +
                ", title='" + title + '\'' +
                '}';
    }

    public void play() {
        System.out.println("播放器开始播放2");
        compactDisc.sing();
    }
}
```
属性源文件
```
disc.title = zzzzz
disc.name = abcd
```
配置文件

通过@PropertySource("classpath:app.properties")声明属性源

自动注入Environment, 通过其getProperty方法来获取属性源中的值
```java
package com.spring.chapter3.RuntimeValueInjection;


import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.core.env.Environment;

@Configuration
@PropertySource("classpath:app.properties")//声明属性源
public class JavaConfig {

    @Autowired
    Environment environment;

    @Bean
    public CompactDisc compactDisc(){
        return new SgtPeppers();
    }
    //为DCPlayer创建bean
    @Bean
    public MediaPlayer mediaPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc,
                environment.getProperty("disc.name"),
                environment.getProperty("disc.title"));
    }
}
```
测试
```java
package com.spring.chapter3.RuntimeValueInjection;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import static org.junit.Assert.*;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = JavaConfig.class)
public class RuntimeValueInjectionTest {
    @Autowired
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;

    @Test
    public void profilesTest(){
        mediaPlayer.play();
        System.out.println(mediaPlayer.toString());

    }
}
```
结果：
```
播放器开始播放2
sgtPeppers的sing方法
CDPlayer{compactDisc=com.spring.chapter3.RuntimeValueInjection.SgtPeppers@225129c, name='abcd', title='zzzzz'}
```
###  2.5.2 使用属性占位符 
```
属性占位符使用方式：${"xxxx"}
```
示例代码

CDPlayer类
通过@Vlaue("${xxxx}")来获取到properties文件中的属性
```java
package com.spring.chapter3.RuntimeValueInjection.PropertyPlaceholder;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;
    private String name;
    private String title;

    public CDPlayer(CompactDisc compactDisc, @Value("${disc.name}") String name,  @Value("${disc.title}")String title) {
        this.compactDisc = compactDisc;
        this.name = name;
        this.title = title;
    }

    @Override
    public String toString() {
        return "CDPlayer{" +
                "compactDisc=" + compactDisc +
                ", name='" + name + '\'' +
                ", title='" + title + '\'' +
                '}';
    }

    public void play() {
        System.out.println("播放器开始播放2");
        compactDisc.sing();
    }
}
```
配置类：
```java
package com.spring.chapter3.RuntimeValueInjection.PropertyPlaceholder;


import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.context.support.PropertySourcesPlaceholderConfigurer;
import org.springframework.core.env.Environment;

@Configuration
@ComponentScan
@PropertySource("classpath:app.properties")//声明属性源
public class JavaConfig {


    @Bean
    public CompactDisc compactDisc(){
        return new SgtPeppers();
    }
 ```
当然，也可以使用XML来使用占位符
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:app.properties" />

    <bean id="sgtPeppers" class="com.spring.chapter3.RuntimeValueInjection.xmlproperties.SgtPeppers" />
    <bean id="cdPlayer" class="com.spring.chapter3.RuntimeValueInjection.xmlproperties.CDPlayer"
          c:_0-ref="sgtPeppers"
          c:_1="${disc.name}"
          c:_2="${disc.title}"
    />
</beans>
```
>注：在《SpringAction》中，说必须要一个PropertySourcesPlaceholderConfigurer bean，但实际情况时，发现根本不需要，也能解析占位符。
### 2.5.3 通过SpEL表达式
根据书上的方式，${"systemProperties['disc.name']"} 并不能获取到值。

经过研究，发现了一种可行的方法

那就是通过environment的getProperty方法来获取到值。SpEL会以environment为bean id 查找对应的bean
```java
package com.spring.chapter3.RuntimeValueInjection.spel;


import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;
    private String name;
    private String title;


    public CDPlayer(CompactDisc compactDisc,
                    @Value("#{environment.getProperty('disc.name')}") String name,
                    @Value("#{environment.getProperty('disc.name')}")String title) {
        this.compactDisc = compactDisc;
        this.name = name;
        this.title = title;
    }

    @Override
    public String toString() {
        return "CDPlayer{" +
                "compactDisc=" + compactDisc +
                ", name='" + name + '\'' +
                ", title='" + title + '\'' +
                '}';
    }

    public void play() {
        System.out.println("播放器开始播放3");
        compactDisc.sing();
    }
}
```
随后又试了xml中配置，发现结果为null！！！！
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:app.properties" />

    <bean id="sgtPeppers" class="com.spring.chapter3.RuntimeValueInjection.spel.SgtPeppers" />
    <bean id="cdPlayer" class="com.spring.chapter3.RuntimeValueInjection.spel.CDPlayer"
          c:_0-ref="sgtPeppers"
          c:_1="#{environment.getProperty('disc.name')}"
          c:_2="#{environment.getProperty('disc.name')}"
    />
</beans>
```
所以！在spel上，还有很长的路要走