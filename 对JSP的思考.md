#   JSP本质上是一个Servlet

 用户在浏览器地址栏上输入http://localhost:8080/dage/index.jsp，web服务器判断用户请求的资源是dage应用中的index.jsp页面，web服务器在dage应用中找到index.jsp，启动JSP翻译引擎，将index.jsp翻译成index_jsp.java文件，并且将index_jsp.java文件编译生成index_jsp.class字节码文件，将其存储在Tomcat服务器work目录中。index_jsp.class类继承了HttpJspBase，HttpJspBase继承了HttpServlet，所以JSP本质上就是Servlet，和Servlet完全相同。只有用户第一次访问这个JSP或者JSP页面被修改了，才会重新翻译。JSP有三个阶段：翻译（一次），编译（一次），运行（多次）。JSP更改，不需要重启服务器，也不需要重新部署项目。Jsp和Servlet本质上没有区别，***但是JSP和Servlet它们的主要职责是有区别的：JSP主要是提取数据做页面展示，而Servlet主要完成业务逻辑处理以及资源跳转。Servlet是Controller（控制层），JSP是View（展示层）***。项目开发中只使用JSP也可以完成开发，因为JSP最终也是一个java类，可以在JSP中编写JDBC等其他程序，但是这样做就违背了设计原则，我们要把的主要职责凸显出来，它的弱点我们还是要尽量避让的。（JSP和Servlet都是单实例多线程的环境下运行的，JSP和Servlet中出现的实例变量、静态变量都会存在线程安全问题。）

# Jsp和Servlet的对象对比

因为JSP本质上是一个Servlet，所以Servlet使用的方法和对象JSP同样也可以使用，Servlet中经常使用的对象在JSP被设置为内置对象，JSP中可以直接使用这些对象，而不用事先进行声明。

## 四大域对象

​       *域对象的作用:保存数据,获取数据,共享数据.*

page：jsp页面被执行，生命周期开始，jsp页面执行完毕，生命周期结束（jsp当前页面有效）

request：用户发送一个请求，生命周期开始，服务器返回响应，请求结束，生命周期结束（一次请求）

session：用户打开浏览器访问，创建session（生命周期开始），session超时或被声明失效，该对象生命周期结束（一次会话）

application：web应用加载的时候创建，web应用移除或服务器关闭，生命周期结束（当前web应用）

## 九大内置对象

request：代表了客户端的请求信息，主要用于接受通过HTTP协议传送到服务器的数据，request对 象的作用域为一次请求

response：代表的是对客户端的响应，主要是将JSP容器处理过的对象传回到客户端。response对象也具有作用域，它只在JSP页面内有效。

Session：JSP内置对象，它在第一个JSP页面被装载时自动创建，完成会话期管理。从一个客户打开浏览器并连接到服务器开始，到客户关闭浏览器离开这个服务器结束，被称为一个会话。

application：可将信息保存在服务器中，直到服务器关闭，否则application对象中保存的信息会在整个应用中都有效。与session对象相比，application对象生命周期更长，类似于系统的“全局变量”。

out：用于在Web浏览器内输出信息，并且管理应用服务器上的输出缓冲区。

pageContext：可以取得任何范围的参数，通过它可以获取 JSP页面的out、request、reponse、session、application 等对象。

config：取得服务器的配置信息。通过 pageConext对象的 getServletConfig() 方法可以获取一个config对象。

Cookie：一个Web站点可能会为每一个访问者产生一个唯一的ID，然后以Cookie文件的形式保存在每个用户的机器上。

exception：显示异常信息，只有在包含 isErrorPage=”true” 的页面中才可以被使用，在一般的JSP页面中使用该对象将无法编译JSP文件。