<https://www.cnblogs.com/w-wfy/p/6425357.html>

监听器本质上是一个触发器，会捕捉到某些事件，并可以在事件开始和结束的时候执行操作，具体可以分成以下三种情况：

######     　1.   ServletContext监控：对应监控application内置对象的创建和销毁。

　　当web容器开启时，执行contextInitialized方法；当容器关闭或重启时，执行contextDestroyed方法

​     　*实现方式：直接实现ServletContextListener接口：*

```xml
<context-param>
    <param-name>contextConfigLocation</param-name>
     <param-value>classpath:spring/root-context.xml</param-value>
</context-param>
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

Spring监听器的contextInitialized方法会在服务器启动的时候把参数值加载到contextConfigLocation中，为什么<context-param>要与Listener一起使用，因为服务器在启动的时候会去加载web.xml首先加载<context-param>节点，再加载Listener节点，而Listener中会加载<context-param>内部的参数值

2. ###### HttpSession监控：对应监控session内置对象的创建和销毁。

   当打开一个新的页面时，开启一个session会话，执行sessionCreated方法；当页面关闭session过期时，或者容器关闭销毁时，执行sessionDestroyed方法。

   *实现方式：直接实现HttpSessionListener接口*：

3. ###### ServletRequest监控：对应监控request内置对象的创建和销毁。

   当访问某个页面时，出发一个request请求，执行requestInitialized方法；当页面关闭时，执行requestDestroyed方法。

   *实现方式，直接实现ServletRequestListener接口：*