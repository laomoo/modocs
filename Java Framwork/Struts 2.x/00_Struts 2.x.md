# 项目准备

## 1. 手工部署开发环境
### 1.1. 导入架包【架包下载地址：[struts.apache.org](https://struts.apache.org/download.cgi#struts2518)】
- 在lib中有jar包，不能把这些jar都导入到项目中，需要导入如下：<br/>
![](https://ww1.sinaimg.cn/large/005BYqpggy1fxoquzymx7j30ja0cdjt6.jpg)

### 1.2. 创建Web Project工程，创建Action
```java
package com.util.test;

import com.opensymphony.xwork2.ActionSupport;

@SuppressWarnings("serial")
public class TestDemo_00 extends ActionSupport {

	private String username;

	public void setUsername(String username) {
		this.username = username;
	}

	public String getUsername() {
		return username;
	}

	@Override
	public String execute() throws Exception {
		this.username = "Hello，" + username + " ！";
		System.out.println(username);
		return ActionSupport.SUCCESS;
	}
}
```

### 1.3. 配置Action类访问路径
- 创建struts2核心配置文件
	核心配置文件名称和位置是固定的
	位置必须在src下面，名称`struts.xml`

- 引入DTD约束
```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN" "http://struts.apache.org/dtds/struts-2.1.dtd">
```
- Action配置
```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN" "http://struts.apache.org/dtds/struts-2.1.dtd">
<struts>
	<include file="struts-default.xml" />
	<package name="default" namespace="/" extends="struts-default">
		<action name="testDemo_00" class="com.util.test.TestDemo_00">
			<result name="success">testDemo_00.jsp</result>
		</action>
		<action name="action_0000" class="com.util.test.Action_0000">
			<result name="success">/ResponsePage/demo_0000.jsp</result>
			<result name="error">/ResponsePage/demo_0001.jsp</result>
		</action>
	</package>
</struts>    
```

- 配置Struts2过滤器
```XML
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
	version="3.0">
	<display-name>0002_Java Struts</display-name>
	<filter>
		<filter-name>struts2</filter-name>
		<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>struts2</filter-name>
		<url-pattern>*.action</url-pattern>
	</filter-mapping>

	<welcome-file-list>
		<welcome-file>index.jsp</welcome-file>
	</welcome-file-list>
</web-app>
```

### 1.4 Struts 2.x 执行流程
![](https://ws3.sinaimg.cn/large/005BYqpggy1fxoscfaj9yj30y10eb0vk.jpg)

## 2. MyEclipse部署开发环境
### 2.1. 新建工程
![](https://ww1.sinaimg.cn/large/005BYqpggy1fxno3lic0cj30gl0lyjtx.jpg)
### 2.2. 增加 Apache Struts 2.x 依赖
![](https://ww1.sinaimg.cn/large/005BYqpggy1fxno3lu8w4j30s30tbn22.jpg)
### 2.3. 完整项目
![](https://ww1.sinaimg.cn/large/005BYqpggy1fxno3ku4orj309908hjrt.jpg)
### 2.4. 默认配置文件
#### web.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
	version="3.0">
	<display-name>0002_Java Struts</display-name>
	<filter>
		<filter-name>struts2</filter-name>
		<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>struts2</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

	<welcome-file-list>
		<welcome-file>index.jsp</welcome-file>
	</welcome-file-list>
</web-app>
```
#### struts.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN" "http://struts.apache.org/dtds/struts-2.1.dtd">
<struts>
	<include file="struts-default.xml" />
	<package name="default" namespace="/" extends="struts-default">
		<action name="testDemo_00" class="com.util.test.TestDemo_00">
			<result>testDemo_00.jsp</result>
		</action>
	</package>
</struts>    
   
```
#### index.jsp
```xml
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ taglib prefix="s" uri="/struts-tags"%>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
<head>
<title>Say Hello</title>
</head>
<body>
	<h3>Say "Hello" to:</h3>
	<s:form action="testDemo_00" namespace="/">
            Name: <s:textfield name="username" />
		<s:submit />
	</s:form>
</body>
</html>
```
#### TestDemo_00.java
```java
package com.util.test;

import com.opensymphony.xwork2.ActionSupport;

@SuppressWarnings("serial")
public class TestDemo_00 extends ActionSupport {

	private String username;

	public void setUsername(String username) {
		this.username = username;
	}

	public String getUsername() {
		return username;
	}

	@Override
	public String execute() throws Exception {
		this.username = "Hello，" + username + " ！";
		System.out.println(username);
		return ActionSupport.SUCCESS;
	}
}
```
#### testDemo_00.jsp
```xml
<%@ page contentType="text/html; charset=UTF-8" %>
<%@ taglib prefix="s" uri="/struts-tags" %>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
    <head>
        <title>Hello</title>
    </head>
    <body>
        <h3><s:property value="username" /></h3>
    </body>
</html>
```