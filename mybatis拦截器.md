mybatis拦截器本质上使用的是JDK的动态代理

![1556266054061](https://github.com/tangjieyong/DailyConclusion/raw/master/images/1556266054061.png)

方法签名表明了动态代理拦截的对象和方法



- #### plugin  

  ​       为动态代理生成代理对象类似于动态代理的Proxy.newInstance()方   法，mybatis对原生的动态代理方法进行了封装

- #### setProperties

   	将插件注册时 的property属性设置进来

- #### intercept

  ​       拦截目标对象的目标方法进行增强并放行,mybatis在JDK动态代理的invoke方法的基础上进行封装。

## 使用拦截器来修改原sql语句的预编译参数

​       ![1556269523267](https://github.com/tangjieyong/DailyConclusion/raw/master/images/1556269523267.png)

parameterHandler.parameterObject内部封装的是传入的全部参数，若parameteType是javaBean类型那么使用metaObject.getValue()方法获得参数后强转为对应的bean类型，然后修改bean中属性的值，使用metaObject.setvalue()方法修改值

![1556270685688](https://github.com/tangjieyong/DailyConclusion/raw/master/images/1556270685688.png)

若是在原先的sql语句上进行修改，在拦截器内执行修改后的语句并接受其返回结果，要使用delegate.parameterHandler,并调用其setParameter方法，可保留原sql语句的预编译参数的映射

​        