# Spring中读取properties文件主要有两种用途

1. #####   为Spring配置文件中的数据库连接池参数赋值 值来自于properties配置文件

   ```xml
   <!--配置数据库连接池-->
   <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
       <property name="driverClass" value="${jdbc.driver}"/>
       <property name="password" value="${jdbc.password}"/>
       <property name="user" value="${jdbc.user}"/>
       <property name="jdbcUrl" value="${jdbc.url}"/>
    </bean>
   ```

2. ##### 为java类中的变量赋值，值来自于properties配置文件

```java
@Value("${Image.savePath}")
private String filePath;
```

#### 

#### 以上两种方式都要事先在Spring配置文件中读取properties配置

```xml
<Context:property-placeholder location="classpath:mybatis/*.properties"/>
```

在一个Spring工程中property-placeholder 只能出现一次，要在一个标签内读取所有的配置文件

可以定义一个总的ApplicationContext.xml在内部读取所有的配置文件，并引入其他所有的Spring配置文件

```xml
<!--该XML文件包含所有的Spring配置文件，在web.xml中加载SpringMVC前端控制器的时候加载当前的配置文件-->

<!--扫描所有的properties文件   只能出现一次-->
<Context:property-placeholder location="classpath:mybatis/*.properties"/>
<!--引入所有的Spring配置文件-->
<import resource="applicationContext-*.xml"/>
```

