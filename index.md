# 欢迎来到candlesky的主页

## Spring的一个核心思路

1.所有类都要装配到bean里面

​	例如

```xml
<bean id="user" class="com.tao.pojo.User">
	<property name="name" value="123"/>
</bean>
```

2.所有的bean都要通过容器去取

​	例如

```java
ApplicationContext cpx = new ClassPathXmlApplicationContext("applicationContext.xml")
```

3.容器里的bean调用出来就是一个对象

​	例如

```java
User user = (User) cpx.getBean("user");
```

- 对象是由Spring创建的

  对象的属性是由Spring容器注入的（DI依赖注入） 

  这个过程为IOC控制反转



## 依赖注入（DI）

1. 构造器注入
2. set注入
3. 命名空间注入等



## 注解开发

### 1.	@AutoWired自动装配

#### 注解开启扫描

```xml
<context:annotation-config />
<context:component-scan base-package="com.tao.pojo" />
```



#### 用法

对类成员变量、方法及构造函数进行标注，完成自动装配

通过 @Autowired的使用来消除 set ，get方法。

@Autowired标注可以放在==成员变量==上，也可以放在成员变量的==set方法上==，也可以放在==任意方法==上表示，自动执行当前方法，==如果方法有参数，会在IOC容器中自动寻找同类型参数为其传值==。



==不使用@AutoWired时==

```properties
<property name="属性名" value=" 属性值"/>    
```



==使用@AutoWired==

#### @AutoWired

在使用@Autowired时，首先在容器中查询对应类型的bean

如果查询结果刚好为一个，就将该bean装配给@Autowired指定的数据

如果查询的结果不止一个，那么@Autowired会根据名称来查找。



#### 补充	@Resource

@Autowired	自动装配先byType

@Resource	自动装配先byName



### 2.	@Component（组件）

为了让Spring扫描	能够识别bean

@Component

```java
@Repository
public class User {
    public String name = "123";
}
```

@component等价于

```xml
<bean id="user" class="com.tao.pojo.User">
</bean>
```

- ==@Repository==	用于pojo、dao的component
- ==@Service==
- ==@Controller==

```java
ApplicationContext cpx = new ClassPathXmlApplicationContext("applicationContext.xml");
User user = (User) cpx.getBean("user");
System.out.println(user.name);
```

这里在applicationContext.xml中寻找user的Bean，但里面没有写<bean />所以@component自动为它注册了

#### @Value

```java
@Repositorypublic class User {    @Value("123")    public String name;}
```

- ==@Value==	Spring里的注入值的注解

- 相当于

  ```xml
  <bean id="user" class="com.tao.pojo.User"></bean>`
  ```

  里面的

  ```xml
  <property name="name" value="123"/>
  ```

- 如果提供==setter==方法==@Value==可以放在setter方法上



### 3.	@Autowired和@Component的区别

- ==@Component==是标注在一个==类==上的，作用是将被标注的类注册在spring容器中，将类的实例化交给spring管理

  - 完成的是bean的==注册==

    

- ==@Autowired==是标注在类中的==成员变量或方法==上

  - 完成的是bean的==注入==
  - 当一个类Class A中需要一个B类型的变量时 在声明变量时加上这个注解  spring会在容器中寻找有没有





## 不用xml配置（用配置类）

本来写在resources里的xml进行配置

now	新建个config包	新建java class

添加==@Configuration==注解

就类似于xml里的<beans>...</beans>



@Configuration + @Bean配置

User.java

```java
public class User {    private String name;    public String getName() {        return name;    }    @Value("123")    public void setName(String name) {        this.name = name;    }}
```

==myConfig.java==

```java
@Configurationpublic class myConfig {    @Bean    public User getUser(){//方便理解getUser可以换成user        return new User();    }}
```

main()

```java
public class test {    public static void main(String[] args) {        ApplicationContext a = new AnnotationConfigApplicationContext(myConfig.class);        User getUser = (User) a.getBean("getUser");        System.out.println(getUser.getName());    }}
```

- 不用加@Component

  - @Configuration会被Spring容器托管
  - 自动注册到容器中
  - @Configuration中有@Component

- 使用@Configuration声明配置类时有两种方法来生成Bean

  - ==方法1:==在配置类中定义一个方法，并使用@Bean注解声明

    使用@Bean的话 bean的id就是标注的方法名，可以在使用@Bean(name="")来设置id

    ```java
    @Configurationpublic class myConfig {    @Bean    public User getUser(){        return new User();    }}
    ```

  - ==方法2:==在User类上使用@Component注解，并在配置类上声明==@ComponentScan("User类的路径")==，这样会自动扫描@Component并生成Bean，==不用@Bean==

    ```Java
    public static void main(String[] args) {    ApplicationContext a = new AnnotationConfigApplicationContext(myConfig.class);    User getUser = (User) a.getBean("user");    System.out.println(getUser.getName());}
    ```

    main里的getBeann变成了"user"（方法1是"getUser"）

    ```java
    @Configuration@ComponentScan("com.tao.pojo")public class myConfig {}
    ```

    ```java
    @Componentpublic class User {    private String name;    public String getName() {        return name;    }    @Value("123")    public void setName(String name) {        this.name = name;    }}
    ```

  - ==以上两种方法==

    @Bean @Component留下一个即可 

    使用前者时可通过==方法名==获取对象，后者可以用==类型小写==获取





## 代理模式

Proxy：代理



- 静态代理
- 动态代理



### 好处

- 真实角色（房东）的操作更纯粹，不用关注公共业务
- 公共交给代理角色（中介）
- 公共业务发生拓展时便于集中管理

### 缺点

一个真实角色会产生一个代理角色（效率低）



### 静态代理

角色分析

- 抽象角色：接口/抽象类
- 真实角色：被代理的角色
- 代理角色：代理真实角色
- 客户：访问代理角色

![image-20210705110544784](C:\Users\CANDLESKY\AppData\Roaming\Typora\typora-user-images\image-20210705110544784.png)



#### 代理前

房东

```java
public class Host implements Rent{    @Override    public void rent() {        System.out.println("房东出租房");    }}
```

客户（寻找房东）

```java
public class Client {    public static void main(String[] args) {        Host host = new Host();        host.rent();    }}
```

#### 静态代理

1.接口

```java
public interface Sell {    public void sell();}
```

2.真实角色

```java
public class Host implements Sell {    @Override    public void sell() {        System.out.println("房东出租房");    }}
```

3.代理角色

```java
public class Proxy implements Sell {//中介接管了出租    //卖家和中介都实现了接口，中介的实现调用卖家的实现    //所以这时候客户调用中介或卖家都行？？？        private Host host;	//中介有卖家名单    public Proxy() {    }    public Proxy(Host host) {        this.host = host;    }//由客户端注入一个host（卖家）对象    //客户选择一个卖家    @Override    public void sell() {        host.sell();//中介sell（）调用卖家的sell（）    }    //额外功能 看房    public void setHouse(){        System.out.println("带客户看房");    }}
```

4.客户端

```java
public class Client {    public static void main(String[] args) {                Host host = new Host();//选择卖家        Proxy proxy = new Proxy(host);//注入到中介        proxy.sell();//调用中介sell（）        proxy.setHouse();    }}
```

- 个人想法
- 所以，客户端只要能注入个host对象就行了
- 所以，把代理的构造方法去掉，换成set，客户端用set方法也可以，就像这样
- 两者的区别是上面的从Proxy proxy = new Proxy(xxx)==构造器（构造方法）==注入，下面用==set方法==注入，所以构造器空参
- 但是这玩意直接可以main里调用商家所以代理有啥用。。。
- 可以不修改源代码情况增加新功能

```java
public class Proxy implements Sell {    private Host host;    public void setHost(Host host) {        this.host = host;    }    @Override    public void sell() {        host.sell();    }}
```

```java
public class Client {    public static void main(String[] args) {        Host host = new Host();        Proxy proxy = new Proxy();        proxy.setHost(host);        proxy.sell();    }}
```



### 动态代理

- 基于接口的动态代理---JDK动态代理

- 基于类的动态代理
- 字节码----javasist

InvocationHandler类：调用处理程序

Peoxy类：提供了创建动态代理实例的静态方法



静态代理的里的类Proxy，在动态代理里自动生成就是了

```java
//用这个类自动生成代理类public class ProxyInvocationHandler implements InvocationHandler {    //这里没有实现接口    //被代理的接口    private Rent rent;    public void setRent(Rent rent) {        this.rent = rent;    }    //生成得到代理类    public Object getProxy(){        return Proxy.newProxyInstance(this.getClass().getClassLoader(),                                      rent.getClass().getInterfaces(),                                      this);    }    //处理代理实例，返回结果    @Override    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {        //反射实现动态代理        Object result = method.invoke(rent, args);        return result;    }}
```

```java
public class Client {    public static void main(String[] args) {        Host host = new Host();        ProxyInvocationHandler pih = new ProxyInvocationHandler();        pih.setRent(host);        Rent proxy = (Rent) pih.getProxy();        proxy.rent();    }}
```



## AOP

面向切面编程

```xml
<dependency>    <groupId>org.aspectj</groupId>    <artifactId>aspectjweaver</artifactId>    <version>1.9.7</version></dependency>
```

### 1、使用Spring的API接口

```xml
<!--注册bean--><bean id="service" class="com.tao.service.ServiceImp"/><bean id="log" class="com.tao.log.log"/><!--配置aop--><aop:config>    <!--切入点-->    <aop:pointcut id="pointcut" expression="execution(* com.tao.service.ServiceImp.*(..))"/>    <!--执行环绕增加-->    <aop:advisor advice-ref="log" pointcut-ref="pointcut"/></aop:config>
```

```java
public class log implements MethodBeforeAdvice {    @Override    //要执行的目标对象的方法   参数  目标对象    public void before(Method method, Object[] objects, Object o) throws Throwable {        System.out.println(o.getClass().getName());    }}
```

```java
public static void main(String[] args) {    ApplicationContext cpx = new ClassPathXmlApplicationContext("applicationContext.xml");    Service service = (Service) cpx.getBean("service");    service.add();}
```

### 2、自定义类实现

```java
public class DiyPointCut {    public void before(){        System.out.println("方法执行前");    }}
```

```xml
<bean id="service" class="com.tao.service.ServiceImp"/><bean id="diy" class="com.tao.diy.DiyPointCut"/><aop:config>    <!--自定义切面，ref要引用的类-->    <aop:aspect ref="diy">        <!--切入点-->        <aop:pointcut id="point" expression="execution(* com.tao.service.ServiceImp.*(..))"/>        <!--通知-->        <aop:before method="before" pointcut-ref="point"/>    </aop:aspect></aop:config>
```

```java
main()//不变
```

### 3、注解实现AOP

```xml
<!--也可以@Component注册--><bean id="service" class="com.tao.service.ServiceImp"/><bean id="annotationPointCut" class="com.tao.diy.AnnotationPointCut"/><!--开启注解支持--><aop:aspectj-autoproxy/>
```

```java
@Aspect//标注这个类是个切面public class AnnotationPointCut {    @Before("execution(* com.tao.service.ServiceImp.*(..))")//切入点    public void before(){        System.out.println("方法执行前");    }}
```

```java
main()//不变
```





## 整合Mybatis



### 回顾Mybatis

1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写Mapper.xml
5. 测试





### sqlSession的创建

> 1、==SqlSessionFactoryBuilder==去读取mybatis的配置文件，
>
> 然后build一个DefaultSqlSessionFactory,即得到==SqlSessionFactory==
>
> ```java
> String resource = "mybatis_config.xml";InputStream inputStream = Resources.getResourceAsStream(resource);sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
> ```

> 2、获取到SqlSessionFactory之后，就可以利用SqlSessionFactory方法的==openSession==来获取==SqlSession对象==
>
> ```java
> public static SqlSession getSqlSession(){ return sqlSessionFactory.openSession();}
> ```

> 3、利用SqlSession内部的方法进行CRUD操作



### 关于SqlSessionFactoryBuilder

在基础的 MyBatis 用法中

![img](https://upload-images.jianshu.io/upload_images/10882211-a81622050f9c887b.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/629/format/webp)

![这里写图片描述](https://img-blog.csdn.net/20170623205900852?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzQxMjc3Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

在 MyBatis-Spring 中，则使用==SqlSessionFactoryBean==来创建。

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">  <property name="dataSource" ref="dataSource" /></bean>
```

由 Spring 最终创建的 bean并不是SqlSessionFactoryBean本身，而是工厂类（SqlSessionFactoryBean）的 getObject() 方法的返回结果，等效于

```java
 return factoryBean.getObject();
```





### 整合Spring与Mybatis

1、导入相关jar包

```xml
<!--spring--><dependency>    <groupId>org.springframework</groupId>    <artifactId>spring-webmvc</artifactId>    <version>5.1.10.RELEASE</version></dependency><!--aop织入,支持切入点表达式等--><dependency>    <groupId>org.aspectj</groupId>    <artifactId>aspectjweaver</artifactId>    <version>1.9.7</version></dependency><!--junit测试--><dependency>    <groupId>junit</groupId>    <artifactId>junit</artifactId>    <version>4.12</version></dependency><!--MySQL数据库驱动--><dependency>    <groupId>mysql</groupId>    <artifactId>mysql-connector-java</artifactId>    <version>8.0.24</version></dependency><!--mybatis--><dependency>    <groupId>org.mybatis</groupId>    <artifactId>mybatis</artifactId>    <version>3.5.2</version></dependency><!--spring管理的jdbc,以及事务相关--><dependency>    <groupId>org.springframework</groupId>    <artifactId>spring-jdbc</artifactId>    <version>5.1.10.RELEASE</version></dependency><!--mybatis与spring整合的jar包--><dependency>    <groupId>org.mybatis</groupId>    <artifactId>mybatis-spring</artifactId>    <version>2.0.6</version></dependency>
```

2、编写数据源配置

3、sqlSessionFactory

4、sqlSessionTemplate

5、给接口加实现类？（多此一举？service层？）

6、将实现类注入到Spring中

（Spring无法注册接口只能注册实现类）

7、测试



- 关于mapper映射文件存放的位置（mapper.xml）

  - 1、在resources下（最好创建同样的目录结构）

  - 2、放在同一文件夹下，同时pom.xml要加入

    ```xml
    <build>    <resources>        <resource>            <directory>src/main/java</directory>            <includes>                <include>**/*.xml</include>            </includes>            <filtering>false</filtering>        </resource>    </resources></build>
    ```



### 整理

这是测试方法 

```java
public void test() throws IOException {    //1、首先从spring-mapper.xml中获取上下文    ApplicationContext cpx = new ClassPathXmlApplicationContext("spring-mapper.xml");    //2、在容器中找到一个id为"userMapper"的bean实例化为userMapper    UserMapper userMapper = (UserMapper) cpx.getBean("userMapper");    //3、调用userMapper对象的查询方法，遍历输出    for (User user : userMapper.selectUser()) {        System.out.println(user);    }}
```

上面第2步要寻找id为"userMapper"的bean

这是spring-mapper.xml的一部分

```xml
<!--3、找到了"sqlSessionFactory"	它被"datasource"数据源注入--><bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">    <property name="dataSource" ref="datasource" />        <property name="configLocation" value="classpath:mybatis-config.xml"/>    <property name="mapperLocations" value="classpath:UserMapper.xml" /></bean><!--2、找到了"sqlSession"	它被"sqlSessionFactory"注入--><bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">   	<constructor-arg index="0" ref="sqlSessionFactory"/></bean><!--构造方法sqlSessionFactory没有set方法可以弄一个实现类在实现类里把sqlSession注入进去所以看第0步 --><!--1、找到了"userMapper"	它被"sqlSession"注入--><bean id="userMapper" class="com.tao.mapper.UserMapperImp">	<property name="sqlSession" ref="sqlSession"/></bean><!--0、"sqlSession"被实现类注入-->
```

实现类

```java
public class UserMapperImp implements UserMapper{    private SqlSessionTemplate sqlSession;    public void setSqlSession(SqlSessionTemplate sqlSession) {        this.sqlSession = sqlSession;    }    @Override    public List<User> selectUser() {        UserMapper mapper = sqlSession.getMapper(UserMapper.class);        //UserMapper.xml的对象？        return mapper.selectUser();    }}
```

