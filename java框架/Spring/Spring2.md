---
title: Spring(2)：高级装配
tgas: 
- 高级装配
categories: 
- Spring 教程
---

## 2.1 环境与profiles

### 2.1.1  基于类级别的profiles
接口1：
```java
package com.spring.profiles.classprofiles;

/**
 * 一个光盘接口
 */
public interface CompactDisc {
    void sing();//光盘的sing功能
}
```
接口1实现：
```java
package com.spring.profiles.classprofiles;

/**
 * CompactDisc实现类
 */
public class SgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("用于DEV的sgtPeppers的sing方法");
    }
}
```
接口2：
```java
package com.spring.profiles.classprofiles;

/**
 * 媒体播放器
 */
public interface MediaPlayer {
    void play(); //一个播放方法
}
```
接口2实现：
```java

package com.spring.profiles.classprofiles;

import org.springframework.beans.factory.annotation.Autowired;

public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    public CDPlayer(CompactDisc compactDisc){
        this.compactDisc = compactDisc;
    }

    public void play() {
        System.out.println("用于dev的CDPlayer");
        compactDisc.sing();
    }
}
```
配置类：
使用了@Profile声明了profile，名为dev，默认是不激活状态，即无法创建bean
```java
package com.spring.profiles.classprofiles;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration
@Profile("dev")
public class ClassprofilesConfig {
    @Bean
    public CompactDisc compactDisc(){
        return new SgtPeppers();
    }

    @Bean
    public MediaPlayer mediaPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc);
    }
}
```
测试：
通过@ActiveProfiles("dev"),激活对应的profiles,处于该profiles下的bean此时可以被spring创建
```java
package com.spring.profiles.classprofiles;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = ClassprofilesConfig.class)
@ActiveProfiles("dev")
public class ClassprofilesTestTest {
    @Autowired
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;

    @Test
    public void profilesTest(){
        mediaPlayer.play();
    }

}
```
### 2.1.2 基于方法级别的profiles
添加一个CompactDisc实例
```java
package com.spring.profiles.methodprofiles;


/**
 * CompactDisc实现类
 */
public class OtherSgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("用于prod的sgtPeppers的sing方法");
    }
}
```
添加一个MediaPlayer实例
```java
package com.spring.profiles.methodprofiles;

public class OtherCDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    public OtherCDPlayer(CompactDisc compactDisc){
        this.compactDisc = compactDisc;
    }

    public void play() {
        System.out.println("用于prod的CDPlayer");
        compactDisc.sing();
    }
}
```
配置类
```java
package com.spring.profiles.methodprofiles;


import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration
public class MethodProfilesConfig {
    @Bean
    @Profile("dev")
    public CompactDisc devCompactDisc(){
        return new SgtPeppers();
    }

    @Bean
    @Profile("dev")
    public MediaPlayer devMediaPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc);
    }
    @Bean
    @Profile("prod")
    public CompactDisc provCompactDisc(){
        return new OtherSgtPeppers();
    }

    @Bean
    @Profile("prod")
    public MediaPlayer ProvMediaPlayer(CompactDisc compactDisc){
        return new OtherCDPlayer(compactDisc);
    }


}
```
测试:根据记过的prod不同，加载不同的bean
```java
package com.spring.profiles.methodprofiles;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import static org.junit.Assert.*;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = MethodProfilesConfig.class)
@ActiveProfiles("prod")
//@ActiveProfiles("dev")
public class MethodProfilesConfigTest {
    @Autowired
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;

    @Test
    public void testProfiles(){
        mediaPlayer.play();
    }

}
```
结果1：
```
WARNING: All illegal access operations will be denied in a future release
用于prod的CDPlayer
用于prod的sgtPeppers的sing方法
```
结果2：
```
WARNING: All illegal access operations will be denied in a future release
用于dev的CDPlayer
用于DEV的sgtPeppers的sing方法
```
### 2.1.3基于XML的profile配置
通过<beans>标签把相同profile放在一起
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <beans profile="dev">
        <bean id="sgtPeppers" class="com.spring.profiles.xmlprofiles.SgtPeppers"/>
        <bean id="cdPlayer" class="com.spring.profiles.xmlprofiles.CDPlayer" c:_0-ref="sgtPeppers"/>
    </beans>

    <beans profile="prod">
        <bean id="otherSgtPeppers" class="com.spring.profiles.xmlprofiles.OtherSgtPeppers"/>
        <bean id="otherCDPlayer" class="com.spring.profiles.xmlprofiles.OtherCDPlayer" c:_0-ref="otherSgtPeppers"/>
    </beans>

</beans>
```
### 2.1.4  基于XML的激活方式
略～～～以后再说
## 2.2 条件化的bean
### 2.2.1 条件化的bean
- 具体步骤
（1）接口
（2）实现接口的类，该类会创建bean
（3）一个实现Condition接口的类，重写matches方法
（4）@Conditional 和@ Bean注解何用，通过matches方法返回的布尔值确定是否创建bean

示例代码
接口：
```java
package com.spring.conditionbean.democondition;

public interface Magic {
    void say();
}

接口实现

package com.spring.conditionbean.democondition;

public class MagicBean implements Magic{
    public void say(){
        System.out.println("no no no");
    }
}
java接口实现
```java
package com.spring.conditionbean.democondition;


import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.env.Environment;
import org.springframework.core.type.AnnotatedTypeMetadata;

public class MagciExistsCondition implements Condition {
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
      return true;
     // return false;
    }
}
```
配置类
通过   @Conditional(MagciExistsCondition.class)把实现加载进来，根据重写的matches方法的返回值确定要不要创建bean
```java
package com.spring.conditionbean.democondition;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Conditional;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration
public class ConditionConfig {
    @Bean
    @Conditional(MagciExistsCondition.class)
    public Magic magic() {
        return new MagicBean();
    }
}
```
测试
这次使用了ApplcationContext来测试是否创建了bean。

Application.containsBean()方法判断是否含有名为magic的bean

asserTrue(boolean condition):如果condition为false，将会抛出AssertionError。如果为true，什么也不会发生
```java
package com.spring.conditionbean.democondition;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import static org.junit.Assert.*;
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = ConditionConfig.class)
public class MagciExistsTest {
    @Autowired
    private ApplicationContext context;
    
    @Test
    public void shouldNotBeNull(){
        assertTrue(context.containsBean("magic"));
    }
}
```
### 2.2.2  matches方法的第一个参数ConditionContext
源码如下
```java
public interface ConditionContext {

//通过返回的BeanDefinitionRegistry检查bean定义
    BeanDefinitionRegistry getRegistry();

//通过返回的ConfigurableListableBeanFactory检查bean是否存在，甚至探查bean的属性
    ConfigurableListableBeanFactory getBeanFactory();

//)通过返回的Environment检查环境变量是否存在以及它的值是什么；
    Environment getEnvironment();

//通过返回的ResourceLoader所加载的资源；
    ResourceLoader getResourceLoader();

//通过返回的ClassLoader加载并检查类是否存在。
    ClassLoader getClassLoader();
}
```
### 2.2.3 matches方法的第二个参数AnnotatedTypeMetadata
源码如下
```java
public interface AnnotatedTypeMetadata {

//检查带有@Bean注解的方法是不是还有其他特定的注解
    boolean isAnnotated(String var1);

//其余的都是用来获取其他注解的属性
    Map<String, Object> getAnnotationAttributes(String var1);

    Map<String, Object> getAnnotationAttributes(String var1, boolean var2);

    MultiValueMap<String, Object> getAllAnnotationAttributes(String var1);

    MultiValueMap<String, Object> getAllAnnotationAttributes(String var1, boolean var2);
}
```
### 2.2.4 条件化的bean与Spring4 的@profile注解
源码如下：
```java

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional({ProfileCondition.class})
public @interface Profile {
    String[] value();
}
```
>可以发现，@Profile注解其实是实现了Conditional注解，且传入的实例为ProfileCondition。

继续前进，进入ProfileCondition.class，源码如下

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package org.springframework.context.annotation;

import java.util.Iterator;
import java.util.List;
import org.springframework.core.type.AnnotatedTypeMetadata;
import org.springframework.util.MultiValueMap;

class ProfileCondition implements Condition {
    ProfileCondition() {
    }

    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        if (context.getEnvironment() != null) {
            MultiValueMap<String, Object> attrs = metadata.getAllAnnotationAttributes(Profile.class.getName());
            if (attrs != null) {
                Iterator var4 = ((List)attrs.get("value")).iterator();

                Object value;
                do {
                    if (!var4.hasNext()) {
                        return false;
                    }

                    value = var4.next();
                } while(!context.getEnvironment().acceptsProfiles((String[])((String[])value)));

                return true;
            }
        }

        return true;
    }
}
```
很明显，该类实现了Condition接口。

首先：通过getAllAnnotationAttributes(Profile.class.getName());获取到了用于@Profile注解的所有属性，那么到底属性指的是什么，通过断点查看下

![在这里插入图片描述](https://img-blog.csdn.net/20180912115343214?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

结果发现，返回的Map中，值是proflie的名称的一个集合(@Profile【({"dev", "asd","ggg"})】

有了值以后，那么就要开始检查以该值为名称的profile到底有没有激活。

首先 进入一遍循环，把profile的第一个值放入value【可以看出，value是一个String[3] 的 数组】

接着，通过Environment的acceptsProfiles方法。该方法用于验证profile是否激活，激活则返回true。
acceptsProfiles源码如下 
```java
public boolean acceptsProfiles(String... profiles) {
//先通过断言判断是否为空
        Assert.notEmpty(profiles, "Must specify at least one profile");
        String[] var2 = profiles;
        int var3 = profiles.length;
//开始循环整个数组，对每一个profiles内的值进行判断
        for(int var4 = 0; var4 < var3; ++var4) {
            String profile = var2[var4];
//StringUtils.hasLength(profile)判断profile是否为空
            if (StringUtils.hasLength(profile) && profile.charAt(0) == '!') {
                if (!this.isProfileActive(profile.substring(1))) {
                    return true;
                }
            } else if (this.isProfileActive(profile)) {
                return true;
            }
        }

        return false;
    }
```
通过acceptsProfiles可以看出，只要有一个profile满足激活，那么就可以返回true。

但是注意：整条语句前加了！

也就是说，当前的profile 为激活时，while中的表达式整体就是false，不再进入循环，执行下一条语句，也就是true。matches返回true的话，bean也就可以被创建。

如果，当前的profile未激活，while中的表达式整体就是true，再次进入循环，判断下一组profile名称是否被激活，一直循环，当所有的profile 名称都被循环了一遍还没有跳出循环，那么说明所有的profile都没被激活，通过
```java
if (!var4.hasNext()) {return false;}
```
返回flase，无法为该@Profile注释的bean创建。
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
## 2.3 处理自动装配歧义
### 2.3.1 标示首选的bean处理歧义
接口1：
```java
package com.spring.ambiguity;

/**
 * 一个光盘接口
 */
public interface CompactDisc {
    void sing();//光盘的sing功能
}
```
接口1的第一个实现
```java
package com.spring.ambiguity;


import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;

/**
 * CompactDisc实现类
 */
@Component
//@Primary
public class SgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("sgtPeppers的sing方法");
    }
}
```
接口1的第二个实现：加入了@Primary，标记成首选bean
```java
package com.spring.ambiguity;


import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;

/**
 * CompactDisc实现类
 */
@Component
@Primary

public class OtherSgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("OtherSgtPeppers的sing方法");
    }
}
```
接口1 的第三个实现
```java
package com.spring.ambiguity;


import org.springframework.stereotype.Component;

/**
 * CompactDisc实现类
 */
@Component
public class AnotherSgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("AnotherSgtPeppers的sing方法");
    }
}
```
接口2
```java
package com.spring.ambiguity;

/**
 * 媒体播放器
 */
public interface MediaPlayer {
    void play(); //一个播放方法
}
```
接口2的实现
```java
package com.spring.ambiguity;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    @Autowired
    public CDPlayer(CompactDisc compactDisc){
        this.compactDisc = compactDisc;
    }

    public void play() {
        System.out.println("CDPlayer");
        compactDisc.sing();
    }
}
```
测试
```java
package com.spring.ambiguity;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import static org.junit.Assert.*;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = AmbiguityConfig.class)
public class AmbiguityConfigTest {
    @Autowired
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;

    @Test
    public void testProfiles(){
        mediaPlayer.play();
    }


}
```
测试结果：注入的是标志为首选Bean的OtherSgtPeppers
```
WARNING: All illegal access operations will be denied in a future release
CDPlayer
OtherSgtPeppers的sing方法
```
>也可以通过xml来标志首选bean【不推荐，自动装配还是用注解吧，这样有点乱】
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com.spring.ambiguity.xmlAmbiguity"/>
    <bean id="sgtPeppers" class="com.spring.ambiguity.xmlAmbiguity.SgtPeppers"/>
    <bean id="otherSgtPeppers" class="com.spring.ambiguity.xmlAmbiguity.OtherSgtPeppers" primary="true"/>
    <bean id="anotherSgtPeppers" class="com.spring.ambiguity.xmlAmbiguity.AnotherSgtPeppers"/>
</beans>
```
### 2.3.2 限定自动装配的bean处理歧义
接下来的自动装配只考虑一种情况，所有bean都用@Component标示，配置类中启动组件扫描

直接通过@Qualifier("bean id") 与@Autowired 组合使用，找到对应的bean id进行注入
```java
package com.spring.ambiguity.quaifier;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;


@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = QuaifierConfig.class)
public class QuaifierConfigTest {
    @Autowired
    @Qualifier("anotherSgtPeppers")
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;

    @Test
    public void testProfiles(){
        compactDisc.sing();
    }
}
```
### 2.3.3 自定义限定符
通过@Component 与 @Qualifier连用，为bean 创建自定义限定符名称
```java
@Component
@Qualifier("oks")
public class AnotherSgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("AnotherSgtPeppers的sing方法");
    }
}
```
测试
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = QuaifierConfig.class)
public class QuaifierConfigTest {
    @Autowired
//    @Qualifier("anotherSgtPeppers")
    @Qualifier("oks")
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;

    @Test
    public void testProfiles(){
        compactDisc.sing();
    }
}
```
### 2.3.4 自定义一个注解
当需要为一个bean定义多个限定符时，又不能使用多个@Qulifier(同名注解只能存在一个)，所以自定义一个注解并使其拥有@Qulifier功能，完美解决这个问题

自定义方式如下
```java
package com.spring.ambiguity.quaifier;

import org.springframework.beans.factory.annotation.Qualifier;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.CONSTRUCTOR, ElementType.FIELD, ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Okss {
}
```
使用这个注解
```java
/**
 * CompactDisc实现类
 */
@Component
//@Qualifier("oks")
@Okss
public class AnotherSgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("AnotherSgtPeppers的sing方法");
    }
}
```
测试
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = QuaifierConfig.class)
public class QuaifierConfigTest {
    @Autowired
//    @Qualifier("anotherSgtPeppers")
    @Okss
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;

    @Test
    public void testProfiles(){
        compactDisc.sing();
    }
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