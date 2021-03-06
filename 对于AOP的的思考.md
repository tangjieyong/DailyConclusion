AOP意为面向切面编程

   在代码编译或者运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面的编程

AOP的实现思想

1. 基于继承，子类继承父类，在子类中重写父类的方法时使用super.方法的方式调用父类的方法，实现动态的将代码加到指定的方法中，使用继承的方式不仅可以在父类方法执行的前后加上逻辑还可以重写父类的方法
2. 使用装饰模式，将原方法所在的类作为成员变量引入新的类中，在新的类中创建方法产生新的逻辑前调用成员变量的方法
3. 使用JDK动态代理模式，在原先类的基础上创建代理对象，重写invoke方法时既可以获得原先类的方法也可以在其中实现对原先方法的重写

AOP实现方式<https://blog.csdn.net/qq_34310242/article/details/78046384>

                  1. JDK动态代理，需要被代理对象实现接口，或者直接为接口生成代理对象
                  2. cglib，cglib是针对类来实现代理的，他的原理是对指定的目标类生成一个子类，并覆盖其中方法实现增强，**但因为采用的是继承，所以不能对final修饰的类进行代理**