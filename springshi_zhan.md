# Spring实战

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

?.用来判断非空值才继续执行后面的方法

操作类的静态方法和变量 `T()`

```xml
<property name="multiplier" value="#{T(java.lang.Math).PI}" />
<property name="randomNumber" value="#{T(java.lang.Math).random()}"
```

SpEl支持简单的运算符和正则表达式

例如验证email,matches返回一个布尔

```xml
<property name="validEmail" value="#{admin.email matches  '[a-zA-Z0-9._%+-]+@[a-zA-z0-9.-]+\\.com'}"
```

查询集合成员

查询人口大于10000的城市

```xml
<property name="bigCities" value="#{cities.?[population > 10000]}" />
```

匹配第一个符合条件的城市
```xml
<property name="bigCities" value="#{cities.^[population > 10000]}" />
```

匹配最后一个符合条件的城市
```xml
<property name="bigCities" value="#{cities.$[population > 10000]}" />
```

投影集合

若只需获得人口城市的名字和状态属性

```xml
<property name="bigCities" value="#{cities.![name+','+static]}" />
```

##2. 最小化Spring XML配置

1. 自动装配(autowire)--减少`<property />`和`<constructor-arg/ >`
2. 自动检测--减少`<bean/>`

###2.1 自动装配

4种类型的自动装配

1. byName: 与Bean有相同的名字/ID进行自动装配

   即其他Bean的id与本Bean的属性的name匹配即可.
   
2. byType: 与Bean的属性具有相同类似的其他Bean自动装配

  其他Bean的类型与本Bean属性类型一致,即可匹配,
  
  但这肯定会引起多个Bean都可匹配,可以为其他不需要的Bean都配置`primay="false",`而首选的可匹配Bean配置`primay="true"`.(默认Bean的primay都为true)
  
3. constructor: 把与Bean的构造器入参具有相同类似的其他Bean自动装配到Bean构造器中

  与byType类似,构造器找到对应的Bean就注入,但仍可能出现多个Bean都适合.
  
4. autodetect: 首次尝试使用constructor进行自动装配,如果失败(没有发现与构造器匹配的Bean),使用byType进行自动装配 

改变默认装配方式,默认为none;

```xml
<beans 
  ...
  default-autowire="byType"
/>
```

混合使用自动装配和显式装配

当byType出现冲突时,可以在本bean中显示的调用其他bean.

而constructor则不能,必须让spring自己去匹配.

###2.2 使用注解装配

spring默认关系注解装配

使用前需要在context命名空间设置

`<context: annotation-config />`

主要介绍三种注解

1. Spring自带的@Autowired

    1. 进行的是byType装配
    2. 不受限于private和set方法/构造函数,可直接在属性上方属性装配
    3. 可设置`@Autowired(required=false)`当装配不成功时,则不装配.但构造器中,只能有一个构造器设置`@Autowired(required=true)`
    4. 当`@Autowired设置多个构造器时`,Spring会从满足装配条件参数最多的构造器.
    5. 当可装配多个Bean时候,可配置`@Qualifier设置特定的bean`
        
        ```java
         @Autowired
         @Qualifier("guitar") //指定ID
         private Instrument instrument;
        ```    
    6. 使用@Qualifier("名称")缩小范围,第5点用指定ID来过滤,其实也可以通过其他名称来过滤.以下两者方法同效
         ```xml
         <bean class="com.springinaction.springidol.Guitar">
           <qualifier value="stringed" />
         </bean>
         ```
         同作用
         ```java
         @Qualifier("stringed")
         public class Guitar implements Instrument{...}
         ```
    7. 自定义限定器

         ```java
         @Target({ElementType.FIELD, ElementType.PARAMETER,ElementType.TYPE})
         @Retenction(RetentionPolicy.RUNTIME)
         @Qualifier
         public @interface StringedInstrument {}
         ```
         
         在其他类中就可以用限定器标记类
         ```java
         @StringedInstrument
         public class Guitar implements Instrument{...}
         ```
         
         在需要的属性中使用
         ```java
         @Autowired
         @StringedInstrument
         private Instrument instrument;
         ```
2. JSR-330的@Inject

    @Inject和@Autowired非常类似
    
    但是@Inject特性有
    
    1. 没有required属性,匹配不上Bean,选择报错
    2. @Named和@Qualifier对应,但@Named只选择Bean ID筛选.
    3. @Qualifier和@Autowired的一样,引用的包不一样而已.
3. JSR-250的@Resource

如何为注解注入表达式

```java
@Value(SpEl表达式)
private String song;
```
###2.3 自动检测Bean

配置文件

```xml
<beans ...>
    <context:component-scan
        base-pachage="com.springinaction.springidol"><!--指定扫描包路径-->
    </context:component-scan>
</beans>
```

默认下Spring扫描一下注解

1. @Component: 通用的构造型注解,标识该类为Spring组件

    在声明类的语句上方@Component或者@Component(指定ID)
2. @Controller: 标识该类为Spring MVC Controller
3. @Repository: 标识将该类定义为数据仓库
4. @Service: 标识将该类定义为服务
5. 使用@Component标注的任意自定义注解

但是使用`@Component`来标注所有Spring Bean会显得很麻烦,有什么过滤方法吗

```xml
<beans ...>
    <context:component-scan
        base-pachage="com.springinaction.springidol"><!--指定扫描包路径-->
        <context:include-filer type="assignable" expression="com.springinaction.springidol.Instrument"
    </context:component-scan>
</beans>
```
识别expression指定路径的类及其派生出来的类为Spring Bean

当然我们也可以使用`context:exclude-filter`进行相关的操作

但是type的属性有哪一些?

1. annotation: 扫描expression指定的类
2. assignable:扫描expression指定的类及其派生类
3. aspectj: 扫描与扫描expression指定的AspectJ表达式匹配的类
4. custom: 使用自定义org.springframework.core.type.TyoeFilter实现类.由expression指定
5. regex: 过滤与expression正则匹配的类

###2.4 Java代码代替xml配置

1. 定义spring指定配置代码
      ```xml
      <beans ...>
          <context:component-scan 
              base-package="com.springinaction.springidol"
      </beans>
    ```
2. 配置java文件

    ```java
    @Configuration
    public class SpringIdolConfig {
        
        //声明ID为duke的JugglerBean
        @Bean
        public Performer duke() {
            return new Juggler();
        }
        
        //依赖注入set
        public Performer kenny() {
            Instrumentalist kenny = new Instrumentalist();
            kenny.setSong("Jiggle Bells");
            return kenny
        }
    }
    ```

##3 面向切面的Spring

###3.1 术语

场景 电力公司查电表.
AOP术语

1. 通知(Advice)

    Spring定义的通知
    
    1. Before
    2. After
    3. After-returning
    4. After-throwing
    5. Around: 在被通知的方法调用前和调用后的行为
2. 连接点(Joinpoint): 应用执行过程中能够插入切面的一个点
3. 切点(Poincut): 定义需要AOP的地方.
4. 切面(Aspect): 通知和切点的结合,共同定义了关于切面的全部内容:它是什么,在何时何处完成其功能
5. 引入(Introduction): 允许我们向现有的类添加新的方法或属性
6. 织入(Weaving): 在目标对象声明周期的那些店织入
    
      1.编译期: AspectJ方式就是在此时织入 
      2.类加载期
      3.运行期: Spring AOP方式.
  
###3.2 使用切点选择连接点

AspectJ切点表达式

1. arg(): 限制连接点匹配参数为指定类型的执行方法
2. @arg(): 限制连接点匹配参数由指定注解标注的执行方法
3. execution(): 用于匹配是连接点的执行方法
4. this(): 限制连接点匹配AOP代理的Bean引用为指定类型的类
5. target(): 限制连接点匹配目标对象为指定类型的类
6. @target(): 限制连接点匹配特定的执行对象,这些对象对应的类要具备指定类似的注解
7. within(): 限制连接点指定的类型
8. @within(): 限制连接点匹配指定注解所标注的类型(使用Spring AOP时,方法定义在指定的注解所标注的类里)
9. @annotation: 限制匹配带有指定注解连接点

Spring自带表达式
bean(可指定id)
###3.3 编写切点

`execute(* com.springinaction.springidol.Instrument.play(..) && within(com.spring.idol.springidol.*))`

上面语句限制的切点范围:

1. `*` 代表不限定返回类型
2. `..` 嗲表不限定参数
3. 当`com.springinaction.springidol.Instrument`执行play方法时
4. 当方法所在类在`com.spring.idol.springidol`包中.

###3.4 在XML中声明切面

1. `<aop:advisor>`: 定义AOP通知器
2. `<aop:after>`: 定义后置通知
3. `<aop:after-returning>`: 定义运行后返回返回值,触发的方法 
4. `<aop:after-throwing>`: 定义运行后异常的处理方法
5. `<aop:around>` :定义AOP环绕通知
  
    环绕的通知作用是:当执行前和执行后有共同变量需要获取,例如要获取方法执行所耗费的时间.
    
    ```java
    public void watch_performance(ProceedingJoinPoint joinpoint) {
        try {
             long start = System.currentTimeMillis();//方法执行前
             joinpoint.proceed();//执行被通知的方法
             long end =System.currentTimeMillis();//方法执行后
        } catch (Throwable t) {
            System.out.println("异常");//表演失败后.
        }
    }
    ```
    
    声明环绕通知
    
    ```xml
    <aop:config>
        <aop:aspect ref="audience">
            <aop:pointcut id="performance2" expression ="execution (* com.springinaction.springidol.Performer.perform(//)) *" />
            <aop:around pointcut-ref="performance2" method ="watch_performace()" />
        </aop:aspece>
    </aop:config>
    ```
6. `<aop:aspect>`:定义切面
7. `<aop:aspectj-autoproxy>`:启动@AspectJ注解驱动的切面
8. `<aop:before>`
9. `<aop:config>`: 顶层的AOP配置元素
10. `<aop:declare-parents>`: 为被通知的对象引入额外的接口,并透明地实现
11. `<aop:pointcut>`:定义切点     

#### 3.4.1 为通知传递参数

假如现在有一名正在想东西的人,我们有一个读心者,需要获取他正在想的东西

```xml
<aop:config>
    <aop:aspect ref="magician"
        <aop:pointcut id="thinking"
            expression="execution(* com.springincation.springidol.Thinker.think_of_something(String)) and (thoughts)"
        />
    <aop:before 
        pointcut-ref="thinking"
        method="interceptThoughts"
        arg-names="thoughts">    
    /aop:aspect>

</aop:config>

```

#### 3.4.2 为切面新增新方法

```xml
<aop:aspect>
    <aop:declare-parents 
        types-matching="com.sprintinaction.springidol.Performer+"
        implement-interface="com.springinaction.springidol.Contestant"
        delegate-ref="contestantDelegate"
    />
</aop:aspect>
```
1. `aop:declare-parents`: 透明的给被通知Bean引入额外的接口
2. `types-matching`: 哪些类会实现`implement-interface`指定的接口.
3. `delegate-ref`: 指定由什么类来实现`implement-interface`接口.
4. `delegate-impl`: 和`delegate-ref`类似,`delegate-ref`指定的Spring Bean,而`delegate-impl`指定特定的类.

#### 3.4.3 注解切面

```java
@Aspect
public class Audienct {
    @Pointcut(execution(* com.springinaction.springidol.Performer.perform(..)) *)//定义切点.
    public void performance(){}//方法内容并不重要,主要用于给@Pointcut注解
    
    @Before("performance()")
    public void task_seats() {
        System.out.println("表演前");
    }
    
    @AfterReturning("performance()")
    public void applaud() {
        System.out.println("表演之后");
    }
    
    @AfterThrowing("performance()")
    public vod demandRefund() {
        System.out.println("出异常拉")
    }
}
```

#### 3.4.5 给注解添加方法
```java
@Aspect
public class ContestanIntroducer {
    @DeclareParents(
        value = "com.springinaction.springidol.Performer+",
        defaultImpl = GraciousContestanct.class
    )
    public static Contestanct contestant;
}
```

### 3.5 注入Aspectj切面

注入Aspectj切面比Spring AOP要强大,Aspectj不用依赖Spring.

当Spring无法解决我们的问题,请用更强大的Aspectj.
```java
public aspect JudgeAspect {
    public JudgeAspect(){}
    
    pointcut performance() : execution(* perform(..));
    
    after returning() : performance() {
        System.out.println(criticism_engine_get_criticism());
    }
    
    //injected
    private CriticismEngine criticism_engine;
    
    public void setCriticismEngine(CriticismEngine criticism_engine) {
        this.criticism_engine = criticism_engine;
    }
}
```

`Judge_Aspect`主要用于比赛后发表言论,言论从`CriticismEngine`中随机获取...

```java
public class CriticismEngineImple implements CriticismEngine {
    public CriticismEngineImpl(){}
    
    public String getCriticism() {
    int i = (int)(Math.random() * criticismPool.length);
    return criticismPool[i];
    }
    
    //injected
    private String criticismPool;
    public void setCriticismPool(String[] criticismPool) {
        this .criticismPool = criticismPool;
    }
} 

```

在Bean注入言论
```xml
<bean id="criticismEngine"
    class="com.springinaction.springidol.CriticismEngineImpl">
       <property name="criticisms">
           <list>
               <value>麦哥牛逼</value>
               <value>麦哥好帅</value>
           </list>
       </property>
 </bean>
```

但是我们的Spring可以使用`Aspect`切面

```xml
<bean class="com.springinaction.springidol.JudgeAspect"
      factory-method="aspectOf">
      <property name="critismEngine" ref="criticismEngine"> 
</bean>
```

因为Aspect切面由AspectJ运行时创建,所以Spring有机会为JudgeAspect注入CriticismEngine时候,JudgeAspect已经被实例化.所以就无法声明JudgeAspect声明为一个Bean.

但是AspectJ提供了静态的`aspectOf()`方法返回切面的实例.

## 4.征服数据库

这里主要讲解了Spring对JDBC模板,Hibernate和JPA的集成应用.

主要讲配置文件如何使用.

本文,主要想探讨AOP的应用方式,所以这部分略过.

## 5. 事务管理

全有或全无的操作称为事务.

事务的ACID

1. 原子性(Atomic): 全部发送/全部不发送
2. 一致性(Consistent): 一旦事务完成,系统涉及到的业务状态一致
3. 隔离性(Isolated): 用户的操作不会与其他用户纠缠在一起
4. 持久性(Durable): 事务完成,数据库也要完成持久化

### 5. 1 定义事务属性

1. 传播行为

     传播规则说明:新事务应该被启动还是被挂起,方法是否要在事务环境中运行.
     1. PROPAGATION_MANDATORY: 表示该方法必须要在事务中运行
     2. PROPAGATION_NESTED: 表示如果当前已经存在一个事务,那么该方法将会在嵌套事务中运行,嵌套事务可以独立于当前事务进行单独的提交/回滚.
     3. PROPAGATION_NEVER: 表示当前方法不应该运行在事务上下文中.
     4. PROPAGATION_NOT_SUPPORTED: 表示该方法不应该运行在事务中,如果存在当前事务,在该方法运行期间,当前事务将会被挂起.
     5. PROPAGATION_REQUIRED: 表示当前方法必须运行在事务中.
     6. PROPAGATION_REQUIRES_NEW: 表示当前方法必须运行在自己的事务中,一个新的事务将会被启动,如果存在当前事务,在该方法执行期间,当前事务会被挂起
     7. PROPAGATION_SUPPORTS: 表示当前方法不需要事务上下文,如果存在当前事务的话,那么该方法会在这个事务中运行.
2. 隔离级别: 定义一个事务可能受其他并发事务的影响程度

  并发可能引起的问题
    
    1. 脏读(Dirty reads): 一个事务读取了另外一个事务改写但尚未提交的数据,如果另一个事务回滚了,那么本身的事务数据是无效的
    2. 不可重复读(Nonrepeatable read): 一个事务执行相同的查询两次或两次以上,但是每次得到都是不同的数据时,通常因为有其他并发事务在更新数据.
    3. 幻读(Phantom read): 与不可重复读类似,例如T1事务读取了几行数据,接着T2事务插入了数据,而随后的查询中,T1会发现原本不存在的记录.
    
 隔离级别的定义 

    1. ISOLATION_DEFAULT: 使用数据库默认的隔离级别
    2. ISOLATION_READ_UNCOMMITTED: 允许读取尚未提交的数据变更
    3. ISOLATION_READ_COMMITTED: 允许读取并发事务已经提交的数据,可以防止脏读,但是不可重复读和幻读仍有可能.
    4. ISOLATION_PEPEATABLE_READ: 对统一字段的多次读取结果是一致的,除非数据是被本身事务所修改,可以防止脏读和不可重复读,但是幻读仍有可能.
    5. ISOLATION_SERIALIZABLE: 完成服从ACID级别,可防止脏读、不可重复读和幻读,但是这是最慢的,因为它通常通过完全锁定事务相关的数据库表来实现.
3. 是否只读: 只对数据库进行读操作,可以用于优化,因为只读优化实在事务启动时由数据库实施的,所以只对启动一个新事务的传播行为(PROPAGATION_REQUIRED、PROPAGATION_REQUIRES_NEW以及PROPAGATION_NESTED)才有意义
4. 事务超时: 事务超时,超过特定秒数就会回滚,只对启动一个新事务的传播行为(PROPAGATION_REQUIRED、PROPAGATION_REQUIRES_NEW以及PROPAGATION_NESTED)才有意义
5. 回滚规则: 默认发生运行时异常,会回滚,也可指定特定异常回滚/不回滚

配置文件指定隔离级别

```xml
<tx:advice id="txAdvice">
  <tx:attributes>
      <tx:method name="save" propagation="REQUIRED" read-only="true"/>
      ...
  </tx:attributes>
</tx:advice>
```

在`<tx:method>`定义隔离级别

1. isolation: 指定事务隔离级别
2. propagation: 定义事务传播规则
3. read-only: 是否只读
4. rollback-for/no-rollback-for: 指定特定异常回滚/不回滚
5. timeout: 指定超时时间

在AOP中设定事务

```xml
<aop:config>
  <aop:advisor
    pointcut="execution(* *..SpitterService.*(..))"
    advice-ref="txAdvice" />
</aop:config>
```

### 5.2 注解事务

指定事务管理器,查找Spring上下文查找`@Transactional`注解的Bean

```xml
<tx:annotation-driven transaction-manager="txManager" />
```

```java
@Transactional(propagation="Propagation.SUPPORTS, readOnly=true")
public class SpitterServiceImpl implements SpitterSevice {
   
   @Transactional(propagation=Progation.REQUIRED,readOnly=false)
    public vod addSpitter(Spitter spitter) {...}
}
```

## 6. 使用Spring MVC

本章主要讲解视图层的各种标签使用.

但是笔者比较推崇前后端分离,严重拒绝这种前后端紧紧耦合的设计.

所以这里没细读.

## 7. Spring Web Flow

这里主要介绍了`Spring Web Flow`框架.

其主要实现是把一个模块的逻辑都编写在xml中,发送的情况可以在xml文件中一目了然,这种方式其实有点模块化,就是把特定的一系列行为封装起来.

还是很不错的.

## 8. 保护Spring应用

介绍了`Spring Security`,主要通过注解方式对系统进行维护,主要介绍了

1. 使用Servlet过滤器波阿虎WEB应用
2. 基于数据库和LDAP进行认证
3. 透明地对方法调用进行保护

## 9. 使用远程服务

里面罗列了RMI,Hessian,Burlap等远程服务.

笔者对这些并不怎么感冒,,为啥不直接通过HTTP API访问.....

## 10.为Srping添加REST功能

REST(Pepresentational State Transfer)表述性状态转移

本章介绍了RESTful一些比较好的入门介绍和Spring服务端/客户端的编写实战.

## 11. Spring消息



