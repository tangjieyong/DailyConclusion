类中某一个方法的参数若想与其他的方法共享，可以将方法的参数定义成成员变量，在该方法中为使用this.成员变量的方式为成员变量赋值，如此其他的方法便可以拿到这个参数，得以实现方法参数的全员共享。

成员变量为类中的所有方法共享，类中某一个方法对成员变量的修改会影响其他方法中成员变量的值，这也是引发并发问题的一个原因。

类中的方法体使用的参数可以来自于类中定义的成员变量也可以来自于方法的参数，若来自于成员变量那么要在变量前加this,方法体中对成员变量的操作主要包括，**调用成员变量的属性或者方法**（自定义变量）,**为成员变量赋值**（基本数据类型成员变量赋值，自定义成员变量创建对象类似于set方法）

类的无参构造引用其他的方法表示在创建对象的时候执行方法，类中的静态方法中引入其他方法表示在类加载完毕后引用方法，静态方法中只能引入静态方法因为类加载完毕后对象可能没有创建，普通方法依赖对象，静态方法依赖类

类中的set方法中调用其他的方法表示在调用set方法完成类中成员变量赋值之后再调用该方法



```java
public void setDbIndex(Integer dbIndex) {
    this.dbIndex = dbIndex;
    count();
}
  public void count(){
        //多次计算总页数，先定义临时变量进行保存，最后再一并赋值
//        计算总页数
        int totalPageTemp=totalCount/pageCapacity;
        //是否有余数
        int plus=(totalCount%pageCapacity)==0? 0:1;
//        最终的页数
        totalPageTemp+=plus;
        if(totalPageTemp<1){
            totalPageTemp=1;
        }
        this.totalPage=totalPageTemp;

//        处理当前页数
        if(this.currentPage<=0){
         this.currentPage=1;
        }
        if(this.currentPage>this.totalPage){
         this.currentPage= this.totalPage;
        }

//        设置limit 参数
        this.dbIndex=this.pageCapacity;
        this.dbNumber=(this.currentPage-1)*this.pageCapacity;

    }

```

Java高层类的属性和方法参数使用的都是接口类型，高层类在调用其他的高层类时会在构造方法或者方法参数中传递接口的实现类



类中基本数据类型的成员变量可以看作是该类对象的属性。底层的基本类，类中的属性都是基本数据类型，较为高层的类，类属性一般都是引用数据类型，可以理解为高层类是对基本类的封装与集成，将其他的类作为成员变量引入是为了获得该成员变量的属性或者方法，需要注意的是，使用引用类型的成员变量的方法要先为其实例化对象，否则会报空指针异常，，实例化的方式包括即时实例化和延迟实例化两种方式，即时实例化表现为在类的方法中为变量实例化，延迟实例化主要表现为在类的有参构造中为成员变量实例化，即高层类在创建对象时为成员变量实例化。