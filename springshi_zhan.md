# Spring实战

Spring要做什么

1. 基于POJO的轻量级和最小侵入性编程
2. 通过依赖注入和面向接口实现轻耦合
3. 基于切面和管理进行声明式编程
4. 通过切面和模板减少样板式代码

##1. 装配Bean

要实例化一个会朗诵诗歌的杂技员

1.  杂技员:
```java
public class Juggler implements Performer {
        private int beanBags = 3;

        public Juggler(){}

        public Juggler (int beanBags) {
            this.beanBags = beanBags;
        }

        public void perform throws PerformanceException {
            System.out.println("juggling " + beanBags + " beanbags");
        }
}
```

2.  朗诵诗歌员
```java
public interface Poem{
    void recite();
}
publi class Sonnet29 implements Poem {
    private static String[] LINES = ["诗歌"];
    
    
}
```