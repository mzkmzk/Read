# Spripublng实战

Spring要做什么

1. 基于POJO的轻量级和最小侵入性编程
2. 通过依赖注入和面向接口实现轻耦合
3. 基于切面和管理进行声明式编程
4. 通过切面和模板减少样板式代码

##1. 装配Bean

###1.1 声明Bean
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
  
  public class Sonnet29 implements Poem {
        private static String[] LINES = {"诗歌"};

        public Sonnet29(){}

        public void recite() {
          System.out.println(LINES[0]);
        }
  }
  ```
3. 会朗诵诗歌的杂技师

  ```java
  public class PoeticJuggler extends Juggler {
      private Poem poem ;

      public PoeticJuggler(Poem poem) {
          super();
          this.poem = poem
      }

      public perform() throws PerformanceException {
          super.perform();
          System.out.println("while reciting..");
          poem.recite();
      } 
  }
  ```
  
4. Bean装载
    
    ```xml
    <bean id="sonnet29"  class="com.springinaction.springidol.Sonnet29"/>
    
    <bean id="poeticDuke" class="com.springinaction.springidol.PoeicJuggler"
      <constructor-arg value="15" />
      <constructor-arg ref="sonnect29" /> 
    />
    ```

那么如果我们的方法为单例呢?


```java
public class Stage {
  private Stage(){}
  
  private static class StageSingletonHolder {
    static Stage instance = new Stage();
    
    public static Stage getInstance (){
      return StageSingletonHolder.instance;
    }
  }
}
```

使用工厂方法创建Bean

```xml
<bean id="theStage" 
    class="com.springinaction.springidol.Stage"
    factory-method="getInstance"
/>
```

###1.2 Bean的作用域

Spring Bean默认都是单例 修改作用域只需修改scope属性

1. singleton: 单例(默认)
2. prototype: 每次调用创建一个实例
3. request: 在一次http请求中,Bean对应一个实例
4. session: 在一个HttpSession钟 每个Bean为一个实例
5. global-session: 在一个全局的HTTP session中,每个Bean对应一个实例

###1.3 初始化和销毁Bean

