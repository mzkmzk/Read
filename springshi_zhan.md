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

例如一个演唱会Bean,在实例化之前需要开灯,摧毁前关灯.

```xml
<bean id="auditorium" 
    class="..."  
    init-method="turnOnLights" 
    destory-method="turnOffLights"
/>
```

假如在一个上下文中,所有bean都需要在初始化/销毁前执行异议的方法

```xml
<beans ...
    default-init-method="turnOnLights" 
    default-destory-method="turnOffLights"
/>    

```

###1.4 给set属性注入Bean/普通值

```xml
<bean id="piano" class="com.springinaction.springidol.Piano"/>
<bean id="kenny" 
  class="com.springinaction.springidol.Instrumentalist"
  <property name="song" value="Jingle Bells"/>
  <property name="instrument" ref="piano" />
 />
```
###1.5 注入内部Bean

假如kenny想用自己的piano,需要注入内部Bean如何做?
```xml
<bean id="kenny" 
  class="com.springinaction.springidol.Instrumentalist"
  <property name="song" value="Jingle Bells"/>
  <property name="instrument" >
    <bean class="com.springinaction.springidol.Piano">
  </property>
 />
```

###1.6 装配集合

之前装配都是Bean/基本类型,那么List,map,set,props这些怎么办

List,set:

```xml
<bean
  id="hank"
  class="com.springinaction.springidol.OneManBand"
  <property name="instruments">
      <list>
        <ref bean="guitar" />
        <ref bean="cymbal" />
      </list>
  </property>
/>
```

装配Map,Map的key和value都是任意类型的,所以对应的有`key`,`key-ref`,`value`,`value-ref`
```xml
<bean
  id="hank"
  class="com.springinaction.springidol.OneManBand"
  <property name="instruments">
      <map>
        <entry key="grutar" value-ref="guitar"/>
      </map>
  </property>
/>
```
装配props,key和value都必须是string

```xml
<bean
  id="hank"
  class="com.springinaction.springidol.OneManBand"
  <property name="instruments">
      <props>
        <prop key="grutar">guitar</prop>
      </props>
  </property>
/>
```
###1.7 装配空值

```xml
<property name="someNonNullProperty"><null/></property>
```

###1.8 SpEl表达式

语法: `#{Spel表达式}`

引用Bean

```xml
<property name="instrument" value="#{saxophone}" />
```

等价于

```xml
<property name="instrument" ref="saxophone" />
```

额,就是可以用value来注入Bean

可以这么玩,有两名格式kenny和carl,kenny唱啥,carl也会跟着唱啥

```xml
<bean id="carl" class="com.springinaction.springidol.Inturmentalist"
    <property name="song" value="#{kenny.song}" />
</bean>
```

carl要改变注意了,有一个帮忙选择歌曲的类songSelector,carl要根据他来选择歌

```xml
<property name="song" value="songSelector.selectSong()" />
```

carl,只看得懂大写的歌啊.....

```xml
<property name="song" value="songSelector.selectSong().toUpperCase()"
```

Doctor麦说过啥..不要相信任何程序员写的东西....这里selectSong返回的可能是个null,这样再调用toUpperCase明显会报空指针啊..

```xml
<property name="song" value="songSelector.selectSong()?.toUpperCase()"
```

