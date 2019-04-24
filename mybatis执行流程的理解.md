mybtis是基于JDBC的轻量级封装，底层调用的还是JDBC的方法，大体上还是那几个步骤：



1. 加载数据库驱动

2. 获得连接Connection

3. 预编译参数PrepareStatement.set()

4. 执行sql  PrepareStatement.excute()/excuteQuery()

5. 获得返回结果集

只是在mybatis中，数据库驱动在全局配置文件中，预编译sql语句在mapper配置文件中，预编译参数由外界传入，返回的结果集要被映射为bean

mybatis通过一些重要的对象来完成上述操作

- ```java
  SqlSessionFactory sqlSessionFactory=new SqlSessionFactoryBuilder().build(stream);
  ```

​    读取mybatis全局配置文件和mapper.xml并将配置文件的所有标签值封装在`Configuration`对象中，*将insert|update|delete|select标签内的所有标签封装在MapperStatement对象中*，configuration中有一个MapperStatements的map类型属性

```java
public final class MappedStatement {

  private String resource;
  private Configuration configuration;
  private String id;
  private Integer fetchSize;
  private Integer timeout;
  private StatementType statementType;
  private ResultSetType resultSetType;
  private SqlSource sqlSource;
  private Cache cache;
  private ParameterMap parameterMap;
  private List<ResultMap> resultMaps;
  private boolean flushCacheRequired;
  private boolean useCache;
  private boolean resultOrdered;
  private SqlCommandType sqlCommandType;
  private KeyGenerator keyGenerator;
  private String[] keyProperties;
  private String[] keyColumns;
  private boolean hasNestedResultMaps;
  private String databaseId;
  private Log statementLog;
  private LanguageDriver lang;
  private String[] resultSets;
```

![1556110278525](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556110278525.png) 由会话工厂开启一个会话 

![1556110576274](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556110576274.png)

![1556110667479](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556110667479.png) 在Configuration中默认使用Simple类型的Executor



![1556066689102](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556066689102.png) 从Configuration中拿到Environment，根据Environment创建TransactionFactory，Configuration创建Executor 

![1556066762956](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556066762956.png)

创建一个SimpleExecutor 

![1556066948327](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556066948327.png)

为SimpleExecutor 的成员变量赋值

![1556066979034](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556066979034.png) ![1556111820165](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556111820165.png)

![1556111857011](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556111857011.png)



![1556067155321](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067155321.png)  返回DefaultSqlSession,里面包含Configuration和SimpleExecutor 



  ![1556066338782](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556066338782.png)



  ​            mybatis根据传入的接口类型生成动态代理对象，这里的动态代理被代理的不是接口的实现类而是接口本身，本质上不是在原有实现类的基础上进行AOP增强，而是获得传入接口的方法，获得接口中的方法名称，去mapper.xml中找到对应的方法名的id标签中的sql语句

  ![1556067382997](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067382997.png)

  ![1556067416069](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067416069.png)

  ![1556067448910](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067448910.png)

  ![1556067533228](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067533228.png)

  ![1556067731169](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067731169.png)

  ![1556090667877](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556090667877.png)

  ![1556090726988](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556090726988.png)

  使用代理对象执行接口中的方法触发了动态代理中的强化方法，把Method封装为MapperMethod![1556090796516](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556090796516.png)



  先去缓存中查看若缓存中存在就直接从缓存中获得，如果缓存中不存在封装一个MapperMethod再存入缓存中![1556090939327](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556090939327.png)



  ![1556091009336](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091009336.png)

  获得所执行接口方法所在的包名和方法名，去Configuration的MappedStatements（map集合），根据包名和方法名的组合去查找对应的MapperedStatement(一条sql语句及其所包含的全部标签组成一个MapperedStatement，配置文件中有多个mapper.xml，每一个Mapper.xml中有多个sql语句，因此Configuration采用map的方式保存，key为mapper.xml中的namespace+id,也就是接口所在的包名加接口中的方法名，value为MapperedStatement对象，里面封装了sql模块的所有标签值)![1556113776229](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556113776229.png)





  ![1556091139590](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091139590.png)

  获得方法传入的参数，也就是sql语句的预编译参数![1556091256364](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091256364.png)

  封装MapperMethod之后，MapperMethod执行execute方法其中的args是方法的接口方法的参数数组，MapperMethod内部已经封装过![1556091370673](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091370673.png)

  根据方法的CommandType的不同来调用不同的增删改查方法，传入方法的名称以及方法的参数，之前先将参数数组转化为param类型的map![1556091425083](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091425083.png)

  ![1556091482680](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091482680.png)



  ![1556091582170](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091582170.png)

  调用executor的update方法，注意这里的细节，在update的参数中传入的又是Configuration中根据key获得的MapperStatement对象![1556091610608](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091610608.png)

  ![1556091699606](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091699606.png)

  执行之前先清一下缓存（数据库的update。delete操作都要清空一下缓存）![1556092616476](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556092616476.png)

  ![1556092694500](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556092694500.png)



  主要这里的Statement是JDBC中的（跟源码重点看）![1556092741742](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556092741742.png)

  ![1556092880557](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556092880557.png)

  ![1556092962614](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556092962614.png)

  ![1556092996685](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556092996685.png)

  ![1556093061233](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556093061233.png)

  ![1556093147062](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556093147062.png)

  ![1556093167607](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556093167607.png)

  ![1556093235006](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556093235006.png)

  ![1556093251089](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556093251089.png)

  ![1556093343650](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556093343650.png)

  ![1556093440005](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556093440005.png)

  ![1556093532986](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556093532986.png)

  ![1556093965061](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556093965061.png)

  ![1556094079418](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556094079418.png)

  ![1556094105944](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556094105944.png)

  ![1556094195054](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556094195054.png)

  ![1556094224393](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556094224393.png)

  ![1556094293213](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556094293213.png)

  ![1556094321029](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556094321029.png)

  ![1556094353840](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556094353840.png)  使用JDBC的PreparedStatement执行  











  ​                         代理对象执行接口中对应的方法会触发动态代理中的invoke方法，将原生的method包装成mapperMethod()

```java
public MapperMethod(Class<?> mapperInterface, Method method, Configuration config) {
  this.command = new SqlCommand(config, mapperInterface, method);
  this.method = new MethodSignature(config, method);
}
```

先进缓存查看

```java
private MapperMethod cachedMapperMethod(Method method) {
  MapperMethod mapperMethod = methodCache.get(method);
  if (mapperMethod == null) {
    mapperMethod = new MapperMethod(mapperInterface, method, sqlSession.getConfiguration());
    methodCache.put(method, mapperMethod);
  }
  return mapperMethod;
}
```



​      包装结束执行execute()方法

```java
  return mapperMethod.execute(sqlSession, args);
```

​     根据mapperMethod方法的标签类型选择执行对应的增删改查方法

```java
public Object execute(SqlSession sqlSession, Object[] args) {
  Object result;
  if (SqlCommandType.INSERT == command.getType()) {
    Object param = method.convertArgsToSqlCommandParam(args);
    result = rowCountResult(sqlSession.insert(command.getName(), param));
  } else if (SqlCommandType.UPDATE == command.getType()) {
    Object param = method.convertArgsToSqlCommandParam(args);
    result = rowCountResult(sqlSession.update(command.getName(), param));
  } else if (SqlCommandType.DELETE == command.getType()) {
    Object param = method.convertArgsToSqlCommandParam(args);
    result = rowCountResult(sqlSession.delete(command.getName(), param));
  } else if (SqlCommandType.SELECT == command.getType()) {
```

  本质上使用的是Excutor来执行，参数ms里面包含sql语句所在的标签信息， wrapCollection(parameter)包含传入的参数。

​      

```
public int update(String statement, Object parameter) {
  try {
    dirty = true;
    MappedStatement ms = configuration.getMappedStatement(statement);
    return executor.update(ms, wrapCollection(parameter));
  } catch (Exception e) {
    throw ExceptionFactory.wrapException("Error updating database.  Cause: " + e, e);
  } finally {
    ErrorContext.instance().reset();
  }
}
```

![1556026825030](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556026825030.png)





![1556026899251](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556026899251.png)



![1556026936818](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556026936818.png)



![1556027169504](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556027169504.png)



