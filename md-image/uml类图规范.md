###泛化
> 
>![Alt text](https://img-blog.csdn.net/20161205103413880)
>

###实现
> 
>![Alt text](https://img-blog.csdn.net/20161204124436841)
>
>

###依赖
>①局部变量 
>②通过方法获得对象引用
>③对一个类静态方法的调用

```java
public class Students extends Person {
   /** @param bicycle
    */
   public int moveSpeed(Bicycle bicycle) {
      return bicycle.travelSpeed();
   }
}
```
>![Alt text](https://img-blog.csdn.net/20161205103645029)


###关联
>①这里表示Employee可以有0个或更多的TimeCard对象。但是，每个TimeCard只从属于单独一个Employee。
>
>②无箭头表示双向
>
>③表示两个类级别相同
>
>④比如人和家庭住址

![Alt text](http://www.uml.org.cn/oobject/images/image005.gif)


###聚合
>①是关联的一种形式
>
>②表示两个类是整体和局部的关系，整体比局部更高一级别
>
>③部分可以脱离整体存在 比如人和车
>![Alt text](http://www.uml.org.cn/oobject/images/image006.gif)

###合成
>①是聚合的一种形式
>
>②非共享 用private
>
>③整体负责局部的生命周期
>
>④例如人和手指
>![Alt text](http://www.uml.org.cn/oobject/images/image007.gif)
>
