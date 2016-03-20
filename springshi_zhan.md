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

### 3.4.1 为通知传递参数

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

### 3.4.2 为切面新增新方法

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


