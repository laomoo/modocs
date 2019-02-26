# Struts 2.x 基础总结

Struts 2.x 执行流程：<br/>
![](https://ws3.sinaimg.cn/large/005BYqpgly1fxq55195oxj30ia0lfmzj.jpg)

- Struts 2.x 框架应用`JavaEE`三层框架结构中的Web层框架
- Struts 2.x 框架在`Struts 1`和`WebWork`的基础上全新框架
- Web常用框架：① `Struts 2.x` ② `SpringMVC`

## 1. Struts 配置文件

!> `Struts`的配置文件存在两份：分别是配置`Action`的`struts.xml`文件和配置全局的`struts.properties`文件。

`Struts`框架按如下顺序加载配置文件：
1. default.properties
2. struts-default.xml：文件保存在`struts-core-x.x.x.jar`文件中
3. struts.xml：文件是Web应用默认的`Struts`配置文件
4. struts.properties：文件是Web应用默认的`Struts`配置文件
5. web.xml：文件是web应用的配置文件

`【注意】若存在多个文件配置同组常量，则后面的文件的配置会覆盖前面的文件的配置`


### 1.1. struts.xml 属性配置
```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN" "http://struts.apache.org/dtds/struts-2.3.dtd">
<struts>
    <!-- 所有匹配*.action的请求都由struts2处理 -->
    <constant name="struts.action.extension" value="action" />
    <!-- 是否启用开发模式 -->
    <constant name="struts.devMode" value="true" />
    <!-- struts配置文件改动后，是否重新加载 -->
    <constant name="struts.configuration.xml.reload" value="true" />
    <!-- 设置浏览器是否缓存静态内容 -->
    <constant name="struts.serve.static.browserCache" value="false" />
    <!-- 请求参数的编码方式 -->
    <constant name="struts.i18n.encoding" value="utf-8" />
    <!-- 每次HTTP请求系统都重新加载资源文件，有助于开发 -->
    <constant name="struts.i18n.reload" value="true" />
    <!-- 文件上传最大值 -->
    <constant name="struts.multipart.maxSize" value="104857600" />
    <!-- 让struts2支持动态方法调用 -->
    <constant name="struts.enable.DynamicMethodInvocation" value="true" />
    <!-- Action名称中是否还是用斜线 -->
    <constant name="struts.enable.SlashesInActionNames" value="false" />
    <!-- 允许标签中使用表达式语法 -->
    <constant name="struts.tag.altSyntax" value="true" />
    <!-- 对于WebLogic,Orion,OC4J此属性应该设置成true -->
    <constant name="struts.dispatcher.parametersWorkaround" value="false" />
    <!-- 改变Struts标签库对应UI的默认主题 -->
    <constant name="struts.ui.theme" value="simple"/>

    <package name="basePackage" extends="struts-default">

    </package>
</struts>
```
### 1.2. struts.xml 示例配置
```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN" "http://struts.apache.org/dtds/struts-2.0.dtd" >
<struts>

    <constant name="" value="" />
    <!-- include节点是struts2中组件化的方式 可以将每个功能模块独立到一个xml配置文件中 然后用include节点引用 -->
    <include file="struts-default.xml"></include>
    
    
    <!-- package提供了将多个Action组织为一个模块的方式
        package的名字必须是唯一的 package可以扩展 当一个package扩展自
        另一个package时该package会在本身配置的基础上加入扩展的package
        的配置 父package必须在子package前配置 
        name：package名称
        extends:继承的父package名称
        abstract:设置package的属性为抽象的 抽象的package不能定义action 值true:false
        namespace:定义package命名空间 该命名空间影响到url的地址，例如此命名空间为/test那么访问是的地址为http://localhost:8080/struts2/test/XX.action
     -->
    <package name="com.kay.struts2" extends="struts-default" namespace="/test">
        <interceptors>
            <!-- 定义拦截器 
                name:拦截器名称
                class:拦截器类路径
             -->
            <interceptor name="timer" class="com.kay.timer"></interceptor>
            <interceptor name="logger" class="com.kay.logger"></interceptor>
            <!-- 定义拦截器栈 -->
            <interceptor-stack name="mystack">
                <interceptor-ref name="timer"></interceptor-ref>
                <interceptor-ref name="logger"></interceptor-ref>
            </interceptor-stack>
        </interceptors>
        
        <!-- 定义默认的拦截器 每个Action都会自动引用
         如果Action中引用了其它的拦截器 默认的拦截器将无效 -->
        <default-interceptor-ref name="mystack"></default-interceptor-ref>
        
        
        <!-- 全局results配置 -->
        <global-results>
            <result name="input">/error.jsp</result>
        </global-results>
        
        <!-- Action配置 一个Action可以被多次映射(只要action配置中的name不同)
             name：action名称
             class: 对应的类的路径
             method: 调用Action中的方法名，默认方法名称为 execute
        -->
        <action name="hello" class="com.kay.struts2.Action.LoginAction" method="">
            <!-- 引用拦截器
                name:拦截器名称或拦截器栈名称
             -->
            <interceptor-ref name="timer"></interceptor-ref>
        
            <!-- 节点配置
                name : result名称 和Action中返回的值相同
                type : result类型 不写则选用superpackage的type struts-default.xml中的默认为dispatcher
             -->
            <result name="success" type="dispatcher">/talk.jsp</result>
            <!-- 参数设置 
                 name：对应Action中的get/set方法 
            -->
            <param name="url">http://www.sina.com</param>
            <exception-mapping name="" exception="" result="">
                异常处理资源
                <param name="参数">参数值</param>
            </exception-mapping>
        </action>
    </package>
</struts>
```
### 1.3. struts.properties 配置详解
```bash
struts.configuration
该属性制定加载Struts 2配置文件的配置文件管理器。
该属性的默认值是org.apache.struts2.config.DefaultConfiguration，默认的配置文件管理器
如果需要实现自己的配置配置文件管理器，开发者可以实现一个实现Configuration接口的类，该类可以自己加载Struts 2配置文件。  

struts.locale
指定Web应用的默认locale。  

struts.il8n.encoding
该常量指定了Web应用的默认编码集。通常，由于使用中文字符集，将其设置为UTF-8。  

struts.objectFactory
指定Struts 2默认的ObjectFactory Bean，该属性默认时Spring。  

struts.objectFactory.spring.autoWire
指定Spring框架的自动装配模式，默认值时name,即默认根据Bean的name属性自动装配。

struts.objectFactroy.spring.useClassCache
指定整合Spring框架时，是否缓存Bean实例，该属性只允许使用true和false，默认值是true。通常不建议修改该属性值得。  

struts.objectTypeDeterminer
指定Struts 2的类型检测机制，通常支持tiger和notiger两个属性值。  

struts.multipart.parser
指定处理multipart/forma-data的MIME类型（文件上传）请求框架，该属性支持cos、pell、和jakarta等属性，即分别对应使用cos的文件上传框架、pell上传及common-fileupload文件上产框架。默认值是jakarta.  

struts.multipart.saveDir
指定上传文件的临时保存路径，默认值是javax.servlet.context.tempdir。  

struts.multipart.maxSize
该属性指定Struts 2文件上传中整个请求内容允许的最大字节数。  

struts.custom.properties
指定Struts 2 应用加载用户自定义的属性文件，该自定义属性文件指定的属性不会覆盖struts.properties文件中指定的属性。如果需奥加载多个自定义属性文件，多个自定义属性文件的文件名以英文逗号（,）隔开。  

struts.mapper.class
指定将HTTP请求映射到指定Action的映射器，Struts 2提供了默认的映射器:org.apache.struts2.dispatcher.mapper.DefaultActionMapper。默认映射器根据请求的前缀与Action的name属性完成映射。  

struts.action.extension
指定需要Struts 2处理的请求后缀，默认值是action，即所有匹配*.action的请求都都由Struts2处理。如果用户需要指定多个请求后缀，则多个后缀之间以英文逗号（,）隔开。

struts.serve.satic
该属性设置是否通过JAR文件提供静态内容服务，该属性只支持true和false,默认值是false。  

struts.serve.static.browserCache
该属性设置浏览器是否缓存静态内容。当应用处于开发间段时，我们希望每次请求都获得服务器的最新响应，则可设置该属性为false。

struts.enable.DynamicMethodInvocation
设置Struts 2是否支持动态方法调用，默认值是true。  

struts.enable.SlashesInActionNames
设置Struts 2是否允许Action名中使用斜线，默认值为false。  

struts.tag.altSyntax
指定是否允许在Struts 2标签中使用表达式语法，因为通常都会在标签中使用表达式语法，因此应该设置为true，默认值是true。  

struts.devMode
指定Struts 2应用是否应用开发模式。如果该属性设置为true，则可以在应用出错时显示更多、更友好的出错提示。该属性只接受true和false，默认值是false。

struts.il8.reload
设置是否每次HTTP请求到达时，系统都重新加载资源文件。默认值是false。在开发阶段应该设置为true。每次请求都加载资源文件会大大降低应用的性能。  

struts.ui.theme
指定试图标签默认的试图主题，默认值是xhtml。  

struts.ui.templateDir
指定试图主题所需要模板文件的位置，默认值是tempate，即默认加载template路径下的模板文件。  

struts.ui.templateSuffix
指定模板文件的后缀，默认值是ftl。该属性还允许使用ftl、vm、jsp，分别对应FreeMarker、velocity、 JSP模板。  

struts.configuration.xml.reload
设置当struts.xml文件改变后，系统是否自动重新加载该文件。默认值是false。  

struts.velocity.configfile
指定Velocity框架所需的velocity.properties文件位置。默认值为velocity.properties。  

struts.velocity.contexts
指定Velocity框架的Context位置，如果该框架有多个Context，则多个Context之间以英文逗号隔开。  

struts.velocity.toolboxlocation
指定Velocity框架的toolbox位置。  

struts.url.http.port
指定web应用所在的监听端口。  

struts.url.https.port
该属性类似于struts.url.http.port属性的作用，区别是该属性指定的是WEB应用的加密服务端口。  

strust.url.includeParames
该属性指定Struts 2生成URL时是否包含请求参数。该属性接受none、ge 、all三个属性，分别对应不包含、仅包含GET类型请求参数和包含全部请求参数。  

struts.custom.il8n.resources
该属性指定struts 2应用所需要的国际化资源文件，如果有多份国际化资源文件，则多个国际化资源文件的文件名以英文逗号隔开。

struts.dispatcher.parametersWorkaround
对于某些JAVA EE服务器，不支持HttpServletRequest调用getParameterMap()方法，此时可以设置该属性值为true来解决该问题。默认值是false。对于WebLogic、Orion、OC4J服务器，通常应该设置为true。  

struts.freemarker.manager.classname
指定Struts 2使用的FreeMarker管理器。默认值是org.apache.struts2.views.freemarker.FreemarkerManager，这是Struts2内建的FreeMarker管理器。

struts.xslt.nocache
指定XSLT Result是否使用样式表缓存。当应用处于开发间段时，通常被设置为true。  

struts.configuration.files
指定struts2框架默认加载的配置文件，如果需要指定默认加载多个配置文件，则多个配置文件的文件名以英文逗号隔开。默认值为struts-default.xml,strust-plugin.xml,struts.xml。
```

## 2. Action 使用详解
### 2.1 Action的三种编写方式
- 创建普通类，这个不继承任何类，不实现任何接口
```java
public class ActionTest {

    // TODO
}
```
- 创建类，实现接口 Action
```java
public class ActionTest implements Action {

    @Override
    public String execute() throws Exception {
        // TODO
    }
}
```
- 创建类，继承类 ActionSupport（一般使用）
```java
public class ActionTest extends ActionSupport {

    @Override
    public String execute() throws Exception {
        // TODO
    }
}
```

### 2.2 访问 Action 的其他方法（三种方式实现）
#### 2.2.1. 使用action标签的method属性，在属性里面写执行的action的方法
```XML
    <package name="default" namespace="/" extends="struts-default">
        <action name="testDemo_00" class="com.util.test.TestDemo_00">
            <result name="success">testDemo_00.jsp</result>
        </action>
        <!-- method 方法指定需要执行的方法函数名称 -->
        <action name="action_0000" class="com.util.test.Action_0000" method="customExecute">
            <result name="success">/ResponsePage/demo_0000.jsp</result>
            <result name="error">/ResponsePage/demo_0001.jsp</result>
        </action>
    </package>
```
#### 2.2.2. 使用通配符方式实现`重点`
    
    在action标签里面name属性，name属性值里面写 符号`*`星号（表示匹配任意内容）

```XML
<package name="users" namespace="/users" extends="default">     
　　<action name="*_*" class="action.{1}Action" method="{2}">     
　　　　<result name="login_success">/users/Users_login_success.jsp</result>     
　　　　<result name="login_failure">/users/Users_login.jsp</result>     
　　　　<allowed-methods>login</allowed-methods>     
　　</action>  
</package>
```
#### 2.2.3. 动态访问实现（理论不用）
```XML
<!-- 调用动态方法需要设置常量 -->
<constant name="struts.enable.DynamicMethodInvocation" value="true"/>
```
前台页面写法：
```XML
<h3>用户管理</h3>
<a href="${pageContext.request.contextPath}/userAction!save.action">添加用户</a>
<a href="${pageContext.request.contextPath}/userAction!delete.action">删除用户</a>
<a href="${pageContext.request.contextPath}/userAction!charge.action">修改用户</a>
<a href="${pageContext.request.contextPath}/userAction!find.action">查询用户</a>
```
### 2.3 Action 获取表单提交数据
    之前web阶段，提交表单到servlet里面，在servlet里面使用request对象里面的方法获取，getParameter，getParameterMap。
    提交表单到action，但是action没有request对象，不能直接使用request对象。

`action`获取表单提交数据主要三种方式：
- 使用ActionContext类
- 使用ServletActionContext类
- 使用接口注入方式

#### 2.3.1. 使用ActionContext类获取

    因为方法不是静态的方法，需要创建ActionContext类的对象
    这个ActionContext类对象不是new出来的

```java
ActionContext actionContext = ActionContext.getContext();
Map<String, Object> keysMap = actionContext.getParameters();
Set<String> keySet = keysMap.keySet();

for (String key : keySet) {
    System.out.println(keysMap.get(key));
}
```

#### 2.3.2. 使用ServletActionContext类获取

```java
HttpServletRequest request = ServletActionContext.getRequest();
HttpServletResponse response = ServletActionContext.getResponse();
HttpSession session = ServletActionContext.getPageContext().getSession();
PageContext pageContext = ServletActionContext.getPageContext();
ServletContext servletContext = ServletActionContext.getServletContext();
```
#### 2.3.3. 使用接口注入（了解）

- 通过继承`ServletContextAware`,`ServletRequestAware`,`ServletResponseAware`接口来实现`Servlet`对象的获取。

```java
package com.util.test;

import javax.servlet.ServletContext;
import javax.servlet.ServletRequest;
import javax.servlet.http.HttpServletRequest;

import org.apache.struts2.interceptor.ServletRequestAware;
import org.apache.struts2.util.ServletContextAware;

public class TestDemo_01 implements ServletContextAware, ServletRequestAware {

    private ServletContext  servletContext  = null;
    private ServletRequest  request         = null;

    @Override
    public void setServletContext(ServletContext servletContext) {
        this.servletContext = servletContext;
    }

    @Override
    public void setServletRequest(HttpServletRequest request) {
        this.request = request;
    }

    public void setRequest(ServletRequest request) {
        this.request = request;
    }

    public ServletRequest getRequest() {
        return request;
    }

    public ServletContext getServletContext() {
        return servletContext;
    }
}
```
### 2.3.4. Action 操作域对象
```java
//操作三个域对象
//1 request域
HttpServletRequest request = ServletActionContext.getRequest();
request.setAttribute("req", "reqValue");

//2 session域
HttpSession session = request.getSession();
session.setAttribute("sess", "sessValue"); 

//3 ServletContext域
ServletContext context = ServletActionContext.getServletContext();
context.setAttribute("contextname", "context");
```

## 3. Result 页面配置
### 3.1. 全局结果页面

    相同的页面配置可以使用全局结果页面，类似404等等 ...

```XML
        <!-- 全局results配置 -->
        <global-results>
            <result name="input">/error.jsp</result>
        </global-results>
```
### 3.2. 局部结果页面

    配置全局页面，同事也配置局部页面，最终以局部配置为准
```XML
    <package name="default" namespace="/" extends="struts-default">

        <!-- 全局结果页面 -->
        <global-results>
            <result name="input">/error.jsp</result>
        </global-results>

        <action name="testDemo_00" class="com.util.test.TestDemo_00">
            <result name="success">testDemo_00.jsp</result>
        </action>
        <action name="action_0000" class="com.util.test.Action_0000" method="execute">
            <!-- 局部结果页面 -->
            <result name="success">/ResponsePage/demo_0000.jsp</result>
            <result name="error">/ResponsePage/demo_0001.jsp</result>
        </action>
    </package>
```
### 3.3 Result 标签`Type`属性（重要）
```XML
<result-types>
    <!-- 请求转发到另一个Action  -->
    <result-type name="chain" class="com.opensymphony.xwork2.ActionChainResult"/>
    <!-- 默认的类型，相当于servlet的foward,服务器端跳转。客户端看到的是struts2中配置的地址，而不是真正页面的地址。一般用于跳转到JSP页面 -->
    <result-type name="dispatcher" class="org.apache.struts2.dispatcher.ServletDispatcherResult" default="true"/>
    <!--  -->
    <result-type name="freemarker" class="org.apache.struts2.views.freemarker.FreemarkerResult"/>
    <!-- 返回报文头给用户 -->
    <result-type name="httpheader" class="org.apache.struts2.dispatcher.HttpHeaderResult"/>
    <!-- 页面重定向，客户端跳转，用于跳转到JSP -->
    <result-type name="redirect" class="org.apache.struts2.dispatcher.ServletRedirectResult"/>
    <!-- 页面重定向，客户端跳转，用于跳转到Action -->
    <result-type name="redirectAction" class="org.apache.struts2.dispatcher.ServletActionRedirectResult"/>
    <!-- 一般用于下载文件用 -->
    <result-type name="stream" class="org.apache.struts2.dispatcher.StreamResult"/>
    <!-- 用于与Velocity技术的集成 -->
    <result-type name="velocity" class="org.apache.struts2.dispatcher.VelocityResult"/>
    <!-- XSLT(xslt)  -->
    <result-type name="xslt" class="org.apache.struts2.views.xslt.XSLTResult"/>
    <!-- 普通文本 -->
    <result-type name="plainText" class="org.apache.struts2.dispatcher.PlainTextResult" />
</result-types>
```
`struts.xml`对应类型配置
```XML
<action name="action_0000" class="com.util.test.Action_0000" method="execute">
    <result name="success" type="dispatcher">/ResponsePage/demo_0000.jsp</result>
    <result name="error" type="redirect">/ResponsePage/demo_0001.jsp</result>
    <result name="input" type="chain">action_0001</result>
</action>
<action name="action_0001" class="com.util.test.Action_0000" method="execute">
    <result name="success" type="redirectAction">action_0000</result>
</action>
```

## 4. 模型驱动

- 原始方式获取表单封装（会用）
- 属性封装获取表单封装（会用）
- 模型驱动方式获取表单（重点）
- 表达式封装（会用）


    原先，可以直接把表单提交属性封装到action的属性里面
    实现步骤：
    1. 在action成员变量位置定义变量（ 变量名称和表单输入项的name属性值一样）
    2. 变量名称和表单输入项的name属性值一样
    3. 使用属性封装获取表单数据到属性里面，不能把数据直接封装到实体类对象里面

    现在，使用模型驱动方式，也可以直接把表单数据封装到实体类对象里面
    实现步骤：
    1. action实现接口ModelDriven
    2. 实现接口里面的方法 getModel方法，把创建对象返回
    3. 在action里面创建实体类对象

!> 使用模型驱动和属性封装注意问题：
   获取表单可以使用属性封装胡总和模型驱动封装，但是不能同时使用属性封装和模型驱动封装获取表单数据，如果同时使用，只会执行模型驱动

### 4.1. 原始方式获取表单封装（会用）

```java
package cn.itcast.form;

import javax.servlet.http.HttpServletRequest;
import org.apache.struts2.ServletActionContext;
import com.opensymphony.xwork2.ActionSupport;
import cn.itcast.entity.User;
/**
 * 使用ServletActionContext获取表单数据封装到实体类对象里面
 * @author asus
 */
public class Form4DemoAction extends ActionSupport {

    public String execute() throws Exception {
        //1 获取表单数据
        HttpServletRequest request = ServletActionContext.getRequest();
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        String address = request.getParameter("address");
        
        //2 封装到实体类对象里面
        User user = new User();
        user.setUsername(username);
        user.setPassword(password);
        user.setAddress(address);
        
        System.out.println(user);
        return NONE;
    }
}
```

### 4.2. 属性封装获取表单封装（会用）

```java
package cn.itcast.data;

import com.opensymphony.xwork2.ActionSupport;
/**
 * 使用属性封装获取表单数据
 * @author asus
 */
public class DataDemo1Action extends ActionSupport {

    //1 定义变量
    //变量的名称和表单输入项name属性值一样
    private String username;
    private String password;
    private String address;
    
    //2 生成变量的set和get方法
    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
    
    @Override
    public String execute() throws Exception {
        System.out.println(username+":"+password+":"+address);
        return NONE;
    }
}
```
### 4.3. 模型驱动方式获取表单（重点）
```java
package com.util.test;

import com.opensymphony.xwork2.ActionSupport;
import com.opensymphony.xwork2.ModelDriven;
import com.util.bean.User;

@SuppressWarnings("serial")
public class Action_0002 extends ActionSupport implements ModelDriven<User> {

    private User user;

    @Override
    public String execute() throws Exception {
        return super.execute();
    }

    @Override
    public User getModel() {
        System.out.println(user);
        return user;
    }
}

```
### 4.4. 表达式封装（会用）

```java
package cn.itcast.data;

import com.opensymphony.xwork2.ActionSupport;

import cn.itcast.entity.Book;
import cn.itcast.entity.User;
/**
 * 使用表达式封装数据到实体类对象
 * @author asus
 */
public class DataDemo3Action extends ActionSupport {

    //1 声明实体类
    private User user;
    
    private Book book;
    
    public Book getBook() {
        return book;
    }
    public void setBook(Book book) {
        this.book = book;
    }
    //2 生成实体类变量的set和get方法
    public User getUser() {
        return user;
    }
    public void setUser(User user) {
        this.user = user;
    }

    @Override
    public String execute() throws Exception {
        System.out.println(user);
        System.out.println(book.getBname());
        return NONE;
    }
}
```
相关页面使用表达式进行赋值`form.jsp`：
```XML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
    <form action="${pageContext.request.contextPath }/data3.action" method="post">
        username:<input type="text" name="user.username"/>
        <br/>
        password:<input type="text" name="user.password"/>
        <br/>
        address:<input type="text" name="user.address"/>
        <br/>
        bname: <input type="text" name="book.bname"/>
        <br/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```
### 4.5. 比较表达式封装和模型驱动封装
- 使用表达式封装和模型驱动封装都可以把数据封装到实体类对象里面
- 不同特点：
    - 使用模型驱动只能把数据封装到一个实体类对象里面，在一个action里面不能使用模型驱动把数据封装到不同的实体类对象里面
    - 使用表达式封装可以把数据封装到不同的实体类对象里面

### 4.6. 封装对象到集合里面
#### 4.6.1. 封装数据到Map集合
##### MapAction.java
```java
package cn.itcast.data;

import java.util.Map;
import com.opensymphony.xwork2.ActionSupport;
import cn.itcast.entity.User;
/**
 * 封装数据到map集合
 * @author asus
 */
public class MapAction extends ActionSupport {

    // 1 声明map集合
    private Map<String,User> map;
    public Map<String, User> getMap() {
        return map;
    }
    public void setMap(Map<String, User> map) {
        this.map = map;
    }

    @Override
    public String execute() throws Exception {
        System.out.println(map);
        return NONE;
    }
}
```
##### map.jsp
```XML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
    <form action="${pageContext.request.contextPath }/map.action" method="post">
        <!-- 设置key值 map['key值'] 设置value值 -->
        username:<input type="text" name="map['one'].username"/>
        <br/>
        password:<input type="text" name="map['one'].password"/>
        <br/>
        address:<input type="text" name="map['one'].address"/>
        username:<input type="text" name="map['abcd'].username"/>
        <br/>
        password:<input type="text" name="map['abcd'].password"/>
        <br/>
        address:<input type="text" name="map['abcd'].address"/>
        <br/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```
#### 4.6.2. 封装数据到List集合

```java
package cn.itcast.data;

import java.util.List;
import com.opensymphony.xwork2.ActionSupport;
import cn.itcast.entity.User;
/**
 * 封装数据到list集合
 * @author asus
 */
public class ListAction extends ActionSupport {
    // 1 声明List变量
    private List<User> list;
    // 2 生成get和set方法
    public List<User> getList() {
        return list;
    }
    public void setList(List<User> list) {
        this.list = list;
    }

    @Override
    public String execute() throws Exception {
        System.out.println(list);
        return NONE;
    }
}
```
##### list.jsp
```XML
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
    <form action="${pageContext.request.contextPath }/list.action" method="post">
        <!-- list[0] : 表示list集合中第一个user对象 -->
        username:<input type="text" name="list[0].username"/>
        password:<input type="text" name="list[0].password"/>
        address:<input type="text" name="list[0].address"/>
        
        username:<input type="text" name="list[1].username"/>
        password:<input type="text" name="list[1].password"/>
        address:<input type="text" name="list[1].address"/>
        <input type="submit" value="提交"/>
    </form>
</body>
</html>
```

## 5. Struts 标签库
    在JavaWeb中，Struts2标签库是一个比较完善，而且功能强大的标签库，它将所有标签都统一到一个标签库中，从而简化了标签的使用。
    它还提供主题和模板的支持，极大地简化了视图页面代码的编写，同时它还提供对ajax的支持，大大的丰富了视图的表现效果。
    与JSTL(JSP Standard Library，JSP 标准标签库)相比，Struts2标签库更加易用和强大。
!> 使用标签需要引入Struts2核心Jar包，在Jsp头部，加入如下代码：`<%@ taglib uri="/struts-tags" prefix="s" %>`

### 5.1. 标签库分类
![](https://ww1.sinaimg.cn/large/005BYqpggy1fxnsx25zkyj30nj0gcdgx.jpg)

### 5.2. 标签详细解释
#### 5.2.1. if - elseif - else
```XML
<%--1、if elseif else的使用 --%>
<% //存入请求域中一个学生的成绩
    request.setAttribute("score", 89);
%>
<!-- 判断学生成绩，输出成绩所对应的ABCD -->
<s:if test="#request.score>90">
    优秀
</s:if>
<s:elseif test="#request.score>80">
    良好
</s:elseif>
<s:else>
    一般
</s:else>
```
#### 5.2.2. iterator
```XML
<s:iterator value="customers">
<%--
    s:iterator是struts2的一个迭代标签，它的value属性取值是一个OGNL表达式
    var属性：它的取值就是一个普通的字符串.
    用了var：把每次遍历的对象作为value，把var的值作为key，存入ContextMap中
    没用var：把每次遍历的对象压入栈顶，再下次遍历之前弹栈(从栈顶移走)。
    
    begin   开始遍历的索引
    end     遍历的结束索引
    step    遍历的步长。 
    status  计数器对象
    count   已经遍历的集合元素个数
    index   当前遍历元素的索引值
    odd     是否奇数行
    even    是否偶数行
    first   是否第一行
    last    是否最后一行
--%>
     <TR style="FONT-WEIGHT: normal; FONT-STYLE: normal; BACKGROUND-COLOR: white; TEXT-DECORATION: none">
          <TD><s:property value="custName"/></TD>
          <TD><s:property value="custLevel"/></TD>
          <TD><s:property value="custSource"/></TD>
          <TD><s:property value="custIndustry"/</TD>
          <TD><s:property value="custAddress"/></TD>
          <TD><s:property value="custPhone"/></TD>
     </TR>
</s:iterator>
```
#### 5.2.3. property
- id        可选属性，指定该元素的标识
- default   可选属性，如果要输出的属性值为null，则显示default属性的指定值
- escape    可选属性，指定是否忽略HTML代码
- value     可选属性，指定需要输出的属性值，如果没有指定该属性，则默认输出ValueStack栈顶的值
```XML
<%-- 输出值栈中的值 --%>
<s:property value="custName"/>
```

#### 5.2.4. a
```XML
<%--
    使用struts2的超链接标签发送请求：
    <s:a>连接内容</s:a>
    属性：
    href         指定url路径
    action       请求的动作名称（类名）
    namespace    动作名称所在的名称空间
    id           指定id
    method       指定Action调用方法
 --%>
 <s:a action="addUIUser" namespace="/user">添加用户</s:a>
 <s:a action="editUIUser" namespace="/user"> 编辑用户
     <s:param name="userid" value="%{'1'}"></s:param>
 </s:a>
 <a href="${pageContext.request.contextPath}/user/editUIUser.action?userid=1">原始超链接标签-编辑用户</a>
```
#### 5.2.5. debug
    debug标签用于调试Struts2，使用它会在页面中生成一个debug标签，点击后会显示服务器各种对象信息，包括值栈、ContextMap等
```XML
<s:debug/>
```
#### 5.2.6. date
```XML
<%--s:date标签的使用：
    它是用于格式化输出日期
    name属性：取值是一个ognl表达式，表示要格式化的日期对象
    format属性：指定格式
    var属性：取值是一个普通的字符串。
           把格式化好的日期字符串作为value，把var的取值作为key。存入contextMap中
--%>
<% request.setAttribute("myDate",new Date());
  /*  SimpleDateFormat format = new SimpleDateFormat("yyyy年MM月dd日");
   String date = format.format(new Date());
   out.println(date); */
%>
${requestScope.myDate}<br/>
<s:property value="#request.myDate"/>
<s:date name="#request.myDate" format="yyyy年MM月dd日" var="sdate"/>
<br>
格式化后的日期：<s:property value="#sdate"/>
<br/>
${sdate}
```
#### 5.2.7. url
```XML
<%--
    url标签
    作用：用于存放一个路径
    属性：
        action：动作名称
        namespace：名称空间
        var：取值是一个普通字符串。他会把action和namespace组成一个url作为value，把var的取值作为一个key，存入contextMap中
--%>
<s:url action="addUIUser" namespace="/user" var="myurl"/>
<a href="<s:property value='#myurl'/>">添加用户URL</a>
```
#### 5.2.8. 表单标签

- 表单标签的通用属性

属性            | 主题   | 数据类型 | 说明                                                                   
----            | ---    | ----     | ---                                                                   
title           | simple | String   | 设置表单元素的title属性                                                
disabled        | simple | String   | 设置表单元素是否可用                                                   
label           | xhtml  | String   | 设置表单元素的label属性                                                
labelPosition   | xhtml  | String   | 设置label元素的显示位置，可选值 : top 和 left(默认)                    
name            | simple | String   | 设置表单元素的name属性，与Action中的属性名对应                         
value           | simple | String   | 设置表单元素的值                                                       
cssClass        | simple | String   | 设置表单元素的class                                                    
cssStyle        | simple | String   | 设置表单元素的style属性                                                
required        | xhtml  | Boolean  | 设置表单元素为必填项                                               
requiredpositon | xhtml  | String   | 设置必填标记(默认为*)相对于label元素的位置，可选值：left 和right(默认) 
tabindex        | simple | String   | 设置表单元素的tabindex属性                                             

- `<form>` 标签的常用属性及描述

属性       | 是否必填 |  类型  |                   说明                   
-----------|----------|--------|--------------------------------------
 action    | 否       | String | 指定提交时对应的action，不需要action后缀 
 enctype   | 否       | String | HTML表单enctype属性                      
 method    | 否       | String | HTML表单method属性                       
 namespace | 否       | String | 所提交action的命名空间 

- `<s:submit>` 标签的常用属性

属性       | 是否必填 |  类型  |                   说明                   
-----------|----------|--------|--------------------------------------
 action    | 否       | String | 指定提交时对应的action
 method    | 否       | String | 指定action中调用的方法                

- `<s:password>` 标签的常用属性说明

属性            |                   说明                   
-----------    | --------------------------------------
 Name          | 用于指定密码输入框的名称
 Size          | 用于指定密码输入框的显示宽度，以字符数为单位
 MaxLength     | 用于限定密码输入框的最大输入字符串个数                       
 showPassword  | 是否显示初始值，即使显示也仍为密文显示，用掩码代替

- `<s:radio>` 标签的属性及说明

属性       | 是否必填 |  类型  |                   说明                   
-----------|----------|--------|--------------------------------------
 List      | 是       | Cellection、Map、Enmumeration、Iterator，array | 用于生成单选框中的集合
 listKey   | 否       | String | 指定集合对象中的哪个属性作为选项的value
 listValue | 否       | String | 指定集合对象中的哪个属性作为选项的内容

- `<s:checkboxlist>` 标签的常用属性及说明

属性       | 是否必填 |  类型  |                   说明                   
-----------|----------|--------|--------------------------------------
 name      | 否       | String | 指定该元素的name
 list      | 是       | Cellection、Map、Enmumeration、Iterator，array | 用于生成多选框的集合
 listKey   | 否       | String | 生成checkbox的value属性
 listValue | 否       | String | 生成checkbox后面显示的文字

- `<s:select>` 标签的常用属性及说明

属性       | 是否必填 |  类型  |                   说明                   
-----------|----------|--------|--------------------------------------
 list       | 是       | Cellection、Map、Enmumeration、Iterator，array | 用于生成多选框的集合
 listKey    | 否       | String | 生成选项的value属性
 listValue  | 否       | String | 生成选项的显示文字
 headerKey  | 否       | String | 在所有的选项前再加额外的一个选项作为其标题的value值
 headerValue| 是       | String | 显示在页面中header选项的内容
 Multiple   | 否       | Boolean| 指定是否多选，默认为 false
 emptyOption| 否       | Boolean| 是否在标题和真实的选项之间加一个空选项
 size       | 否       | Int    | 下拉框的高度，即最多可以同时显示多少个选项

#### 5.3. 测试案例
```XML
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib uri="/struts-tags" prefix="s" %>
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>注册界面</title>
    </head>
    <body>
        <s:form action="register" namespace="">
            <s:textfield name="username" label="用户名" key="Switch"/>       
            <s:password name="password" label="密码"/>
            <s:radio name="gender" list="#{'0':'男','1':'女'}" label="性别" value="0" />
            <s:textfield name="age" label="年龄"/>
            <s:select name="city" list="#{'bj':'北京','sh':'上海','gz':'广州','sz':'深圳'}" label="城市" headerKey="-1" headerValue="---请选择城市---" emptyOption="true"/>
            <s:checkboxlist name="hibbies" list="#{'code':'写代码','algorithm':'算法','movie':'电影'}" label="爱好"/>
            <s:checkbox name="married" label="是否已婚" value="true" labelposition="left"/>
            <s:textarea name="description" label="自我介绍" rows="5" cols="20"/>
            <s:file name="phone" label="头像"/>
            <s:submit value="提交"/>
            <s:reset value="重置"/>
        </s:form>
    </body>
</html>
```
`案例界面`<br/>
![](https://ww1.sinaimg.cn/large/005BYqpggy1fxo5yyi6vkj30m70bx3yh.jpg)


## 6. OGNL

     OGNL是Object Graphic Navigation Language（对象图导航语言）的缩写，它是一个开源项目。
     Struts2框架使用OGNL作为默认的表达式语言。

     OGNL不是struts2的一部分，单独的项目，经常和struts2一起使用。

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="/struts-tags" prefix="s"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
    <!-- 使用ognl+struts2标签实现计算字符串长度 
        value属性值：ognl表达式
    -->
    <s:property value="'haha'.length()"/>
</body>
</html>
```

### 6.1. `#` 使用

    等待更新

### 6.2. `%` 使用

    等待更新

## 7. ValueStack 值栈

:bangbang::bangbang: 重要需要掌握 :bangbang::bangbang:

    之前在web阶段，在servlet里面进行操作，把数据放到域对象里面，在页面中使用el表达式获取到，域对象在一定范围内，存值和取值。
    在struts2里面提供本身一种存储机制，类似于域对象，是值栈，可以存值和取值。
    在action里面把数据放到值栈里面，在页面中获取到值栈数据。

     servlet和action区别
     1. Servlet：默认在第一次访问时候创建，创建一次，单实例对象
     2. Action：访问时候创建，每次访问action时候，都会创建action对象，创建多次，多实例对象

     值栈存储位置
     1. 每次访问action时候，都会创建action对象
     2. 在每个action对象里面都会有一个值栈对象（只有一个）

### 7.1. 值栈存放数据

- 常用方式：使用ActionContext类里面的方法得到值栈对象（每个action对象中只有一个值栈对象）

#### 7.1.1. 值栈存放数据多种方式

- 获取值栈对象，调用值栈对象里面的`set`方法

```java
package com.util.test;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.util.ValueStack;

public class Action_0003 implements Action {

    public String execute() throws Exception {
        // 第一种方式：使用值栈对象里面的 set 方法
        // 1. 获取值栈对象
        ActionContext actionContext = ActionContext.getContext();
        ValueStack valueStack = actionContext.getValueStack();
        // 2. 调用 set 方法
        valueStack.set("username", "LaoMo");
        return SUCCESS;
    };
}
```
- 获取值栈对象，调用值栈对象里面的`push`方法

```java
package com.util.test;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.util.ValueStack;

public class Action_0003 implements Action {

    public String execute() throws Exception {
        // 第一种方式：使用值栈对象里面的 set 方法
        // 1. 获取值栈对象
        ActionContext actionContext = ActionContext.getContext();
        ValueStack valueStack = actionContext.getValueStack();
        // 2. 调用 push 方法
        valueStack.push("测试");
        return SUCCESS;
    };
}
```
- 在action定义变量，生成变量的`get`方法

```java
package com.util.test;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.util.ValueStack;

public class Action_0003 implements Action {

    private String userAddress;

    public void setUserAddress(String userAddress) {
        this.userAddress = userAddress;
    }

    public String getUserAddress() {
        return userAddress;
    }

    public String execute() throws Exception {

        // 直接赋值即可
        userAddress = "测试";
        return SUCCESS;
    };
}
```
![](https://ws3.sinaimg.cn/large/005BYqpggy1fxq06pge35j30jn09z0tt.jpg)

#### 7.1.2. 值栈存放对象
```java
package com.util.test;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.util.ValueStack;
import com.util.bean.User;

public class Action_0003 implements Action {

    private User    user;
    
    public void setUser(User user) {
        this.user = user;
    }
    
    public User getUser() {
        return user;
    }

    public String execute() throws Exception {
        userAddress = "测试";
        user = new User();
        user.setUsername("LaoMO");
        user.setPassword("1234#qwer");
        user.setAge("25");
        return SUCCESS;
    };
}
```
![](https://ws3.sinaimg.cn/large/005BYqpggy1fxq0grcnqmj30hv0c0myb.jpg)

#### 7.1.3. 值栈存放`list`集合

```java
package com.util.test;

import java.util.ArrayList;
import java.util.List;

import com.opensymphony.xwork2.Action;
import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.util.ValueStack;
import com.util.bean.User;

public class Action_0003 implements Action {

    private List<User>  userList;

    public List<User> getUserList() {
        return userList;
    }

    public void setUserList(List<User> userList) {
        this.userList = userList;
    }

    public String execute() throws Exception {
        user = new User();
        user.setUsername("LaoMO");
        user.setPassword("1234#qwer");
        user.setAge("25");
        
        userList = new ArrayList<User>();
        userList.add(user);
        return SUCCESS;
    };
}
```
![](https://ws3.sinaimg.cn/large/005BYqpggy1fxq3grmqrvj30ix0e03zs.jpg)

### 7.2. 值栈获取数据
#### 7.2.1. 使用struts2的标签 + ognl表达式获取值栈数据

- 获取字符串
```XML
<s:property value=”ognl表达式”/>
```
- 获取对象
```XML
<s:property value="user.username" />
<s:property value="user.password" />
<s:property value="user.age" />
```
- 获取`list`集合的第一种方式
```XML
<!-- 3 获取值栈list集合数据 -->
<!-- 第一种方式实现  -->
<s:property value="list[0].username"/>
<s:property value="list[0].password"/>
<s:property value="list[0].address"/>
<s:property value="list[1].username"/>
<s:property value="list[1].password"/>
<s:property value="list[1].address"/>
```
- 获取`list`集合的第二种方式
```XML
<!-- 使用struts2标签 类似jstl的foreach标签
     s:iterator：遍历值栈的list集合
  -->
<s:iterator value="list">
   <!-- 遍历list得到list里面每个user对象 -->
   <s:property value="username"/>
   <s:property value="password"/>
   <s:property value="address"/>
</s:iterator>
```
- 获取`list`集合的第三种方式
```XML
<s:iterator value="list" var="user">
    <!-- 
        遍历值栈list集合，得到每个user对象
        机制： 把每次遍历出来的user对象放到 context里面
        获取context里面数据特点：写ognl表达式，
        使用特殊符号 #
     -->
    <s:property value="#user.username"/>
    <s:property value="#user.password"/>
    <s:property value="#user.address"/>
</s:iterator>
```

<hr/>

## 8. 拦截器

 :yum:  Struts 2.x是框架，封装许多功能，Struts 2.x里面封装的功能都是在拦截器里面实现的。<br/>
 :yum:  Struts 2.x定义许多拦截器，但是并不是所有都执行，只是执行默认的拦截器。<br/>
 :yum:  默认拦截器位置：struts.default.xml<br/>
 :yum:  拦截器是在`action`对象创建之后，执行之前

配置拦截器的语法：
```XML
<interceptor-stack name="defaultStack">
    <interceptor-ref name="exception"/>
    <interceptor-ref name="alias"/>
    <interceptor-ref name="servletConfig"/>
    <interceptor-ref name="i18n"/>
    <interceptor-ref name="prepare"/>
    <interceptor-ref name="chain"/>
    <interceptor-ref name="debugging"/>
    <interceptor-ref name="scopedModelDriven"/>
    <interceptor-ref name="modelDriven"/>
    <interceptor-ref name="fileUpload"/>
    <interceptor-ref name="checkbox"/>
    <interceptor-ref name="multiselect"/>
    <interceptor-ref name="staticParams"/>
    <interceptor-ref name="actionMappingParams"/>
    <interceptor-ref name="params">
      <param name="excludeParams">dojo\..*,^struts\..*</param>
    </interceptor-ref>
    <interceptor-ref name="conversionError"/>
    <interceptor-ref name="validation">
        <param name="excludeMethods">input,back,cancel,browse</param>
    </interceptor-ref>
    <interceptor-ref name="workflow">
        <param name="excludeMethods">input,back,cancel,browse</param>
    </interceptor-ref>
</interceptor-stack>

<!-- Basic stack -->
<!-- 最基本操作拦截器栈 -->
<interceptor-stack name="basicStack">
    <interceptor-ref name="exception"/>
    <interceptor-ref name="servletConfig"/>
    <interceptor-ref name="prepare"/>
    <interceptor-ref name="checkbox"/>
    <interceptor-ref name="multiselect"/>
    <interceptor-ref name="actionMappingParams"/>
    <interceptor-ref name="params">
        <param name="excludeParams">dojo\..*,^struts\..*</param>
    </interceptor-ref>
    <interceptor-ref name="conversionError"/>
</interceptor-stack>
```
### 8.1. 拦截器底层原理

    等待更新

### 8.2. 自定义拦截器

 要开发自定义拦截器，需要实现`com.opersymphony.xwork2.interceptor.Interceptor`接口。<br/>
 亦需要继承`AbstractInterceptor`抽象类。

 开发可以继承`MethodFilterInterceptor`实现拦截器支持方法过滤特性`（建议使用该类继承）`
 类代码定义如下：
```java
public interface Interceptor extends Serializable {

    // 初始化拦截器的回调方法
    void init();
    // 销毁拦截器之前的回调方法
    void destory();
    // 拦截器实现拦截的逻辑代码
    String intercept(ActionInvocation invocation) throws Exception;
}
```
:point_right: `init()`：拦截器被实例化之后，被执行之前，系统回调此方法。<br/>
:point_right: `destory()`：拦截器被销毁之前，系统回调此方法。<br/>
:point_right: `intercept()`：用户需要实现的拦截的操作。<br/>

自定义拦截器实现：
#### SimpleInterceptor.java
```java
package com.util.interceptor;

import com.opensymphony.xwork2.ActionInvocation;
import com.opensymphony.xwork2.interceptor.AbstractInterceptor;
import com.util.test.Action_0003;

@SuppressWarnings("serial")
public class SimpleInterceptor extends AbstractInterceptor {

    @Override
    public void init() {
        System.out.println("拦截器初始化 ...");
        super.init();
    }

    @Override
    public String intercept(ActionInvocation invocation) throws Exception {

        Action_0003 action = (Action_0003) invocation.getAction();

        System.out.println("拦截器打印："+action.getUser().getUsername());
        System.out.println("拦截器打印："+action.getUser().getPassword());
        System.out.println("拦截器打印："+action.getUser().getAge());
        
        return null;
    }

    @Override
    public void destroy() {
        super.destroy();
    }
}
```
#### struts.xml
```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN" "http://struts.apache.org/dtds/struts-2.1.dtd">
<struts>
    <package name="default" namespace="/" extends="struts-default">
        <!-- 拦截器需要配置在Action前面 -->
        <interceptors>
            <interceptor name="actionInterceptor_00"
                class="com.util.interceptor.SimpleInterceptor"></interceptor>
        </interceptors>
        <action name="testDemo_00" class="com.util.test.TestDemo_00">
            <result name="success">testDemo_00.jsp</result>
            <!-- 应用自定义拦截器之后，需要手动应用框架自带的默认拦截器 -->
            <interceptor-ref name="defaultStack"></interceptor-ref>
        </action>
        <action name="action_0000" class="com.util.test.Action_0000"
            method="execute">
            <result name="success" type="dispatcher">/ResponsePage/demo_0000.jsp
            </result>
            <result name="error" type="redirect">/ResponsePage/demo_0001.jsp</result>
            <!-- 应用自定义拦截器之后，需要手动应用框架自带的默认拦截器 -->
            <interceptor-ref name="defaultStack"></interceptor-ref>
        </action>
        <action name="Action_0003" class="com.util.test.Action_0003">
            <result>/ResponsePage/demo_0002.jsp</result>
            <!-- 应用自定义拦截器之后，需要手动应用框架自带的默认拦截器 -->
            <interceptor-ref name="defaultStack"></interceptor-ref>
            <interceptor-ref name="actionInterceptor_00"></interceptor-ref>
        </action>
    </package>
</struts>    
```

!> 应用自定义拦截器之后，需要手动应用框架自带的默认拦截器

!> 拦截器需要配置在Action前面

## 9. 异常机制

    如果在action中的execute使用 try...catch 来捕获异常，当捕获指定异常时候，返回对应逻辑视图（完全手动处理异常）
    
    优化：
    我们可以配置异常捕获处理的拦截器执行上述操作。
### 9.1. 声明式异常捕捉

`struts 2.x`的异常捕获是通过`struts.xml`文件中配置，默认`struts-default.xml`中已经开启异常映射。

#### struts-default.xml
```XML
<interceptors>
    <interceptor name="exception" class="com.opensymphony.xwork.interceptor.ExceptionMappingInterceptor" />
    </interceptor>
</interceptors>

<interceptor-stack namae="defaultStack">
    <interceptor-ref name="exception"></interceptor-ref>
</interceptor-stack>
```

## 10. 文件上传

    等待更新

## 11. Ajax 支持

    等待更新


## 10. 参考资料
- [struts2中struts.xml配置文件详解](https://www.cnblogs.com/wkrbky/p/5889328.html)
- [Struts2标签库常用标签](https://blog.csdn.net/q547550831/article/details/53326042)
- [Struts2标签库整理【完整】](https://blog.csdn.net/weixin_39418164/article/details/80062148)
- [struts2：struts.properties配置文件介绍及常量加载顺序](https://www.cnblogs.com/nayitian/archive/2013/12/15/3475408.html)
- [ActionSupport类源码](https://blog.csdn.net/hikvision_java_gyh/article/details/9849561)
- [struts2动态方法调用和通配符配置](https://blog.csdn.net/feinifi/article/details/81113891)