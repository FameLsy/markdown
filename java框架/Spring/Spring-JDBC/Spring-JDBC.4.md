---
title: Spring JdbcDAoSupport 简化操作
tgas: 
- Spring JdbcDAoSupport 简化操作
categories: 
- Spring 
---



# Spring DAO开发（JdbcDAoSupport）

使用Spring管理JdbcTemplate类后，我们需要写如下一步导入jdbc

```java
@Resource(name="jdbcTemplate")
private JdbcTemplate jdbcTemplate;
```
并且在xml配置JdbcTemplate,并传入dataSource
```xml
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"/>
</bean>
```

Spring提供了JdbcDAoSupport，帮我们省去这一步，只需要继承该类
```java
/**
 * @author liisyu
 * @date 2019/3/1
 */
public class DemoDao extends JdbcDaoSupport {
    /**
     * 继承后，使用this.getJdbcTemplate()获取JdbcTemplate
     */
    public void test(){
        this.getJdbcTemplate().update("insert into account values ('liisyu3',?,?)", "测试2",100002);
    }
}
```
并且配置文件，需要为该类注入DataSource
```xml
    <bean id="demoDao" class="com.liisyu.Jdbctest.DemoDao">
        <property name="dataSource" ref="dataSource"/>
    </bean>
```

实际上本质是JdbcDaoSupport帮我们完成了这件事
```java
public abstract class JdbcDaoSupport extends DaoSupport {

	@Nullable
	private JdbcTemplate jdbcTemplate;

    	/**
	 * Set the JDBC DataSource to be used by this DAO.
	 */
	public final void setDataSource(DataSource dataSource) {
		if (this.jdbcTemplate == null || dataSource != this.jdbcTemplate.getDataSource()) {
			this.jdbcTemplate = createJdbcTemplate(dataSource);
			initTemplateConfig();
		}
	}

    	@Nullable
	public final JdbcTemplate getJdbcTemplate() {
	  return this.jdbcTemplate;
	}

...
```