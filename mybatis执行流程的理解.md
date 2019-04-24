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

- ```Java
  SqlSession sqlSession = sqlSessionFactory.openSession(true);
  ```

  返回SqlSession的实现类DefaultSqlSession对象。他里面包含了Executor和Configuration；Executor会在这一步被创建,Executor接口包含操作数据库的API，通过这些API调用底层的JDBC操作![1556066689102](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556066689102.png)![1556066762956](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556066762956.png)

  ![1556066948327](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556066948327.png)

  ![1556066979034](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556066979034.png)

  ![1556067155321](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067155321.png)



  ![1556066338782](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556066338782.png)



  ​            mybatis根据传入的接口类型生成动态代理对象，这里的动态代理被代理的不是接口的实现类而是接口本身，本质上不是在原有实现类的基础上进行AOP增强，而是获得传入接口的方法，获得接口中的方法名称，去mapper.xml中找到对应的方法名的id标签中的sql语句

  ![1556067382997](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067382997.png)

  ![1556067416069](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067416069.png)

  ![1556067448910](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067448910.png)

  ![1556067533228](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067533228.png)

  ![1556067731169](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556067731169.png)

  ![1556090667877](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556090667877.png)

  ![1556090726988](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556090726988.png)

  ![1556090796516](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556090796516.png)



  ![1556090939327](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556090939327.png)



  ![1556091009336](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091009336.png)

  ![1556091061812](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091061812.png)



  ![1556091139590](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091139590.png)

  ![1556091256364](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091256364.png)

  ![1556091370673](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091370673.png)

  ![1556091425083](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091425083.png)

  ![1556091482680](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091482680.png)



  ![1556091582170](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091582170.png)

  ![1556091610608](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091610608.png)

  ![1556091699606](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556091699606.png)

  ![1556092616476](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556092616476.png)

  ![1556092694500](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556092694500.png)



  ![1556092741742](C:\Users\keyon\AppData\Roaming\Typora\typora-user-images\1556092741742.png)

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



