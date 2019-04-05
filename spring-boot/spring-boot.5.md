<!-- 读取自定义properties配置文件 -->
自定义的属性，可以定义在主配置文件中，也可以定义在自定义的配置文件中

# 读取主配值文件中的属性
```
# application.properties
user.name=liisyu
```

只需要通过@Value("${}")读取指定的自定义属性
```
@Value("${user.name}")
private String name
```

# 读取自定义配置文件中的属性

```
# my.properties
user.name=liisyu
user.age=19
user.scor
```

通过@PropertySource读取配置文件
```java
@Component
@PropertySource("classpath:my.properties")
public class User(){
  @Value("${user.name}")
  private String name
}

```

# 读取对象属性

```
# my.properties
user.name=liisyu
user.age=19
user.score=45
```

配置属性类
```java
@Component
@PropertySource("classpath:my.properties")
@ConfigurationProperties("user")//@ConfigurationProperties用于指定读取配置文件中的对象属性
public class User(){
  private String name;
  private int age;
  private int score;
}
```

# 读取List<String>属性

```
# my.properties
school.students[0]=abb
school.students[1]=cdd
school.students[2]=dee
```

配置属性类
```java
@Component
@PropertySource("classpath:my.properties")
@ConfigurationProperties("school")//@ConfigurationProperties用于指定读取配置文件中的对象属性
public class User(){
  private List<String> students;
}
```

# 读取List<Object>属性


```
# my.properties
school.students[0].name=abb
school.students[0].age=13
school.students[0].score=99

school.students[1].name=cdd
school.students[1].age=15
school.students[1].score=23

school.students[2].name=dee
school.students[2].age=17
school.students[2].score=45

```

配置属性类
```java
//无需任何注解
public class Sutdent(){  
  private String name;
  private int age;
  private int score;
}

@Component
@PropertySource("classpath:my.properties")
@ConfigurationProperties("school")
public class School(){
  private List<Student> students;
}
