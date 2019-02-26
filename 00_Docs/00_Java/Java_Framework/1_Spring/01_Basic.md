# 技术详解

!>  `IOC(控制反转)`和`DI(依赖注入)`的区别：<br>
     （1）`IOC`是指把对象创建交给`Spring`进行配置<br>
     （2）`DI`是向类里面的属性中设置属性值
     （3）二者关系：依赖注入不能单独存在，需要在`IOC`基础之上完成操作

## 1. Spring Bean 详解

	Bean的id管理Bean的BeanFactory和ApplicationContext中必须是唯一标识。
	因为通过BeanFactory和ApplicationContext获取Bean的实例时候，用它来做唯一索引。

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
        id:唯一标识Bean
        class:Bean对应实现类(必须全路径，不能相对路径)
        scope:singleton     单例模式(默认值)
              prototype     多例模式
              request       创建对象并且放置对象至request域里面
              session       创建对象并且放置对象至session域里面
              globalSession 创建对象并且放置对象至globalSession域里面
        depends-on:用来初始化使用Bean之前，必须强制执行一个或者多个其他Bean类的初始化
        init-method:初始化Bean时候会调用指定初始化方法
    -->
    <bean id="0000_user" class="personal.laomo.bean.User" scope="singleton" depends-on="0004_date" init-method="init">
        <property name="username" value="LaoMo"></property>
        <property name="password" value="1234#qwer"></property>
    </bean>

    <!-- 使用静态工厂创建对象 -->
    <bean id="0001_user" class="personal.laomo.factory.UserFactory0" factory-method="getBean">
        <property name="username" value="0002_user_UserName"></property>
        <property name="password" value="0002_user_PassWord"></property>
    </bean>

    <!-- 使用实例工厂创建对象 -->
    <bean id="0002_user" class="personal.laomo.factory.UserFactory1"></bean>
    <bean id="0003_user" factory-bean="0002_user" factory-method="getBean">
        <property name="username" value="0003_user_UserName"></property>
        <property name="password" value="0003_user_PassWord"></property>
    </bean>

    <!-- 0000_user初始化之前，先初始化0004_date对应类 -->
    <bean id="0004_date" class="java.util.Date"></bean>
</beans>
```

### 1.1. Bean 初始化

	在Spring的初始化中，初始化Bean的属性有两种方法：
	1. applicationContext.xml配置文档中指定init-method属性完成
	2. 实现org.springframework.beans.factory.InitalizingBean接口

	【注意】init-method属性设置的优先级比实现接口高

#### applicationContext.xml
```XML
<!-- init-method:初始化Bean时候会调用指定初始化方法 -->
<bean id="0000_user" class="personal.laomo.bean.User" scope="singleton" depends-on="0004_date" init-method="init">
    <property name="username" value="LaoMo"></property>
    <property name="password" value="1234#qwer"></property>
</bean>
```
#### User.java
```java
package personal.laomo.bean;

@SuppressWarnings("unused")
public class User {

    private String username;
    private String password;

    public void init() {
        this.username = "未知名称";
        this.password = "1234#qwer";
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getUsername() {
        return username;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getPassword() {
        return password;
    }
}
```

### 1.2. Bean 销毁

	在Spring中，销毁Bean有两种方法：
	1. applicationContext.xml配置文档中指定destory-method属性完成
	2. 实现org.springframework.beans.factory.DisposableBean接口

	【注意】destory-method属性设置的优先级比实现接口高

### 1.3 依赖注入的三种实现方式
- 接口注入
- Set注入

```java
package personal.laomo.bean;

@SuppressWarnings("unused")
public class Student {

    private User user;

    public void setUser(User user) {
        this.user = user;
    }

    public User getUser() {
        return user;
    }
}
```

- 参数构造注入

```java
package personal.laomo.bean;

@SuppressWarnings("unused")
public class People {

    private User user;

    /* 参数构造注入 */
    public People(User user) {
        this.user = user;
    }
}
```
```XML
<!-- 使用参数构造进行依赖注入 -->
<bean id="0005_user" class="personal.laomo.bean.AudiCar">
    <constructor-arg name="carname" value="MyAudi"></constructor-arg>
</bean>
```
### 1.4. 集合注入方式
#### 1.4.1. List
Spring 配置文件：
```XML
<!-- 集合注入之List集合注入 -->
<bean name="000_User" class="personal.laomo.bean.User00">
    <property name="feedom">
        <list>
            <value>Hello</value>
            <value>World</value>
            <value>Test</value>
            <value>1234#qwer</value>
        </list>
    </property>
</bean>
```
Bean 实现类：
```java
package personal.laomo.bean;
import java.util.List;
public class User00 {
    private List<String> feedom;
    public void setFeedom(List<String> feedom) {
        this.feedom = feedom;
    }
    public List<String> getFeedom() {
        return feedom;
    }

    @Override
    public String toString() {
        StringBuffer sbBuffer = new StringBuffer();
        if (feedom != null && feedom.size() > 0) {
            for (String string : feedom) {
                sbBuffer.append(string).append(" ");
            }
            return sbBuffer.toString();
        } else {
            return "No Results";
        }
    }
}
```
打印结果：
```bash
Hello World Test 1234#qwer 
```
#### 1.4.2. Map
#### 1.4.3. Set
#### 1.4.4. Properties

### 1.5. Bean 自动装配的五种模式
- byName 模式
 - 通过`Bean`的属性名字自动装配，配置文档中查找与将要配置的属性同样名字的`Bean`
- byType 模式
 - 通过`Bean`的属性类型自动装配，配置文档中查找与将要配置的属性同样类型的`Bean`
 - 存在多个相同类型的`Bean`，则报错抛出异常
- constructor 模式
 - 通过构造函数参数进行自动装配
```java
    <bean id="0000_user" class="personal.laomo.bean.User" scope="singleton" depends-on="0004_date" init-method="init"
          destroy-method="destory" autowire="no">
        <property name="username" value="LaoMo"></property>
        <property name="password" value="1234#qwer"></property>
    </bean>
```
- autodetect 模式
 - 通过对`Bean`的内部进行检查来选择`constructor`和`byType`
 - 如果先找到`constructor`，则先用`constructor`；如果没有`constructor`，则用`byType`
- no 模式
 - 不适用自动装配

!>  显示的指定依赖，例如`property`和`constructor-arg`元素，总会覆盖自动装配模式