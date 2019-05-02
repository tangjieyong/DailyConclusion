mybtis是基于JDBC的轻量级封装，底层调用的还是JDBC的方法，本质上还是那几个步骤：



1. 加载数据库驱动
2. 创建并获取数据库链接
3. 创建jdbc statement对象
4. 设置sql语句
5. 设置sql语句中的参数(使用preparedStatement)
6. 通过statement执行sql并获取结果
7. 对sql执行结果进行解析处理
8. 释放资源(resultSet、preparedstatement、connection)



# 问题总结

1. 数据库连接，使用时就创建，不使用立即释放，对数据库进行频繁连接开启和关闭，造成数据库资源浪费，影响数据库性能。

　　**设想：**使用数据库连接池管理数据库连接。

1. 将sql语句硬编码到java代码中，如果sql语句修改，需要重新编译java代码，不利于系统维护。

　　**设想：**将sql语句配置在xml配置文件中，即使sql变化，不需要对java代码进行重新编译，Java代码与sql语句分离，*Java代码中可以直接调用增删改查的API，底层做的还是将语句交给JDBC类型的PreparedStatement执行。*

1. 向preparedStatement中设置参数，对占位符号位置和设置参数值，硬编码在java代码中，不利于系统维护。

　　**设想：**将sql语句及占位符号和参数全部配置在xml中。

1. 从resutSet中遍历结果集数据时，存在硬编码，将获取表的字段进行硬编码，不利于系统维护。

　　**设想：**将查询的结果集，自动映射成java对象。

#        原生的JDBC开发模式

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class JdbcTest {
    public static void main(String[] args) {
        //数据库连接
       Connection connection = null;
       //预编译的Statement，使用预编译的Statement提高数据库性能
        PreparedStatement preparedStatement = null;
       //结果集
       ResultSet resultSet = null;
    
    try {
        //加载数据库驱动
        Class.forName("com.mysql.jdbc.Driver");

        //通过驱动管理类获取数据库链接
        connection =  DriverManager.getConnection("jdbc:mysql://120.25.162.238:3306/mybatis001?characterEncoding=utf-8", "root", "123");
        //定义sql语句 ?表示占位符
        String sql = "select * from user where username = ?";
        //获取预处理statement
        preparedStatement = connection.prepareStatement(sql);
        //设置参数，第一个参数为sql语句中参数的序号（从1开始），第二个参数为设置的参数值
        preparedStatement.setString(1, "王五");
        //向数据库发出sql执行查询，查询出结果集
        resultSet =  preparedStatement.executeQuery();
        //遍历查询结果集
        while(resultSet.next()){
            System.out.println(resultSet.getString("id")+"  "+resultSet.getString("username"));
        }
    } catch (Exception e) {
        e.printStackTrace();
    }finally{
        //释放资源
        if(resultSet!=null){
            try {
                resultSet.close();
            } catch (SQLException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }
        if(preparedStatement!=null){
            try {
                preparedStatement.close();
            } catch (SQLException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }
        if(connection!=null){
            try {
                connection.close();
            } catch (SQLException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }

    }

}
    }

```



# mybatis架构设计

​      ![1556174125788](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556174125788.png) 

# mybatis中重要对象的介绍

1. ####    Configuration 

   ​      该对象封装了配置文件的全部信息，包括全局配置文件和mappe配置文件，以及Executor,  ParameterHandler      ResultSetHandler,  StatementHandler等重要对象的实例化

2. ####   MappedStatement

      该对象封装了mapper.xml中一条增删改查语句对应的标签内的全部属性，比如sql语句，预编译参数类型，返回类型

#    mybatis四大基本对象（都可以被拦截）

###      1.   *Executor*

​            内部定义了对数据库的update，update方法，供上层的SqlSession调用，他不直接与数据库发生关系，可作为一个中间的对象将API转换为直接将对应的语句交给JDBC的PreparedSatement执行

###      2.  *ParameterHandler*

​           将传入的参数映射到sql语句的的预编译参数位置  

###      3.  *ResultSetHandler*

​           把sql语句的执行结果映射到用户定义的结果返回类型（JavaBean）

###      4. *StatementHandler*

​          完成预编译语句（从MappedStatement中取出sql语句），进行预编译参数的设置（委托ParameterHandler进行），进行语句处理结果的封装处理（调用ResultSetHandler进行）



#  mybatis的核心逻辑分析

![1556176830649](https://github.com/tangjieyong/DailyConclusion/ra/master/images/1556176830649.png)



![1556110278525](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556110278525.png) 由会话工厂开启一个会话 

![1556110576274](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556110576274.png)

![1556110667479](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556110667479.png) 在Configuration中默认使用Simple类型的Executor



![1556066689102](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556066689102.png) 从Configuration中拿到Environment，根据Environment创建TransactionFactory，Configuration创建Executor 

![1556066762956](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556066762956.png)

创建一个SimpleExecutor 

![1556066948327](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556066948327.png)

为SimpleExecutor 的成员变量赋值

![1556066979034](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556066979034.png) ![1556111820165](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556111820165.png)

![1556111857011](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556111857011.png)



![1556067155321](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556067155321.png)  返回DefaultSqlSession,里面包含Configuration和SimpleExecutor 



  ![1556066338782](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556066338782.png)



  ​            mybatis根据传入的接口类型生成动态代理对象，这里的动态代理被代理的不是接口的实现类而是接口本身，本质上不是在原有实现类的基础上进行AOP增强，而是获得传入接口的方法，获得接口中的方法名称，去mapper.xml中找到对应的方法名的id标签中的sql语句

  ![1556067382997](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556067382997.png)

  ![1556067416069](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556067416069.png)

  ![1556067448910](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556067448910.png)

  ![1556067533228](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556067533228.png)

  ![1556067731169](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556067731169.png)

  ![1556090667877](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556090667877.png)

  ![1556090726988](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556090726988.png)

  使用代理对象执行接口中的方法触发了动态代理中的强化方法，把Method封装为MapperMethod![1556090796516](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556090796516.png)



  先去缓存中查看若缓存中存在就直接从缓存中获得，如果缓存中不存在封装一个MapperMethod再存入缓存中![1556090939327](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556090939327.png)



  ![1556091009336](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556091009336.png)

  获得所执行接口方法所在的包名和方法名，去Configuration的MappedStatements（map集合），根据包名和方法名的组合去查找对应的MapperedStatement(一条sql语句及其所包含的全部标签组成一个MapperedStatement，配置文件中有多个mapper.xml，每一个Mapper.xml中有多个sql语句，因此Configuration采用map的方式保存，key为mapper.xml中的namespace+id,也就是接口所在的包名加接口中的方法名，value为MapperedStatement对象，里面封装了sql模块的所有标签值)![1556113776229](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556113776229.png)





  ![1556091139590](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556091139590.png)

  获得方法传入的参数，也就是sql语句的预编译参数![1556091256364](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556091256364.png)

  封装MapperMethod之后，MapperMethod执行execute方法其中的args是方法的接口方法的参数数组，MapperMethod内部已经封装过![1556091370673](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556091370673.png)

  根据方法的CommandType的不同来调用不同的增删改查方法，传入方法的名称以及方法的参数，之前先将参数数组转化为param类型的map![1556091425083](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556091425083.png)

  ![1556091482680](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556091482680.png)



  ![1556091582170](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556091582170.png)

  调用executor的update方法，注意这里的细节，在update的参数中传入的又是Configuration中根据key获得的MapperStatement对象![1556091610608](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556091610608.png)

  ![1556091699606](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556091699606.png)

  执行之前先清一下缓存（数据库的update。delete操作都要清空一下缓存）![1556092616476](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556092616476.png)

  ![1556092694500](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556092694500.png)



![1556092741742](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556092741742.png) 创建StatementHandler对象

  ![1556151027494](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556151027494.png)

![1556092880557](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556092880557.png) 

  创建StatementHandler对象本质上是创建RoutingStatementHander对象，再创建PreparedStatementHandler对象![1556092962614](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556092962614.png)

  为父类BaseStatement的成员变量赋值，并且将mapperStatement的boundSql的属性取出传递给高层父类的BaseStatementHandler的boundSql属性     ![1556092996685](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556092996685.png) 

![1556151764997](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556151764997.png) 

![1556154153356](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556154153356.png)

。 

![1556152157553](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556152157553.png)

![1556154256057](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556154256057.png)



![1556152247230](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556152247230.png)

![1556152304296](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556152304296.png)

![1556152421610](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556152421610.png)

在BaseStatementHandler中同时实例化ParameterHandle和ResultHandler![1556152471810](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556152471810.png)



  ![1556151661001](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556151661001.png)

实例化StatementHander结束后本质上是返回封装好的RoutineStatementHander并返回，这是一个很重要的类，他可以完成处理sql语句预编译，设置参数等相关工作；在其内部调用ParameterHanler来设置参数，调用ResultSetHandler来完成结果的映射处理

![1556093532986](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556093532986.png) 

 

  接下来进行sql语句的预编译参数设置![1556094079418](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556094079418.png)

  ![1556094105944](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556094105944.png)

  ![1556094195054](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556094195054.png)

  ![1556155823311](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556155823311.png)

![1556155841980](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556155841980.png)

![1556155870524](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556155870524.png)

![1556155925454](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556155925454.png)

![1556155961318](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556155961318.png)

![1556155986864](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556155986864.png)

![1556156010301](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556156010301.png)

![1556156096023](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556156096023.png)



  ![1556094293213](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556094293213.png)

  ![1556094321029](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556094321029.png)

  ![1556094353840](https://github.com/tangjieyong/DailyConclusion/blob/master/images/1556094353840.png)  



![]()













  ​   
