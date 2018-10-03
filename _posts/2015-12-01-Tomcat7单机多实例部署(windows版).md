---
published: true
layout: postlayout
thumbimg: 1346208288725.jpg
categories: 
  - tomcat
tags: 
  - tomcat
title: Tomcat7单机多实例部署(windows版)
---


### 1. 下载Tomcat
		下载Tomcat7到目录：D:\ProgramFiles\Java\tomcat-7.0.64
### 2. 设置Tomcat的CATALINA_HOME环境变量
		设定CATALINA_HOME环境变量值为：D:\ProgramFiles\Java\tomcat-7.0.64
### 3. 删除不必要的目录和文件
		3.1 删除%CATALINA_HOME%\work目录
		3.2 删除%CATALINA_HOME%\webapps下的子目录
		3.3 删除%CATALINA_HOME%\temp下的文件

### 4. 新建多实例的目录
		4.1 新建多实例根目录为：D:\ProgramFiles\Java\tomcat7-multi-ins
		4.2 在多实例根目录下创建一个新的实例目录：D:\ProgramFiles\Java\tomcat7-multi-ins\ins-1
		4.3 拷贝tomcat必要的目录(conf，temp，webapps)到ins-1下，logs目录和work目录会自动创建，因此不需要拷贝
		4.4 在ins-1下新建一个文件作为测试页面（ins-1\webapps\test\index.html）:
        
```html
<html>
	<head>
		<meta charset="UTF-8" >
		<title> Tomcat 1</title>
	</head>

	<body>
		<h1> Hello from Tomcat 1</h1>
	</body>
</html>
```

		4.5 拷贝ins-1完整目录到：D:\ProgramFiles\Java\tomcat7-multi-ins\ins-2以快速新建另一个实例
		4.6 修改ins-2\conf\server.xml的以下三部分的端口值，不要跟ins-1\conf\server.xml冲突：
		
```xml
<Server port="8006" shutdown="SHUTDOWN">
<Connector port="8081" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" />
<Connector port="8010" protocol="AJP/1.3" redirectPort="8443" />
``` 

		4.7 修改ins-2\webapps\test\index.html的内容以作区分

```html
<html>
	<head>
		<meta charset="UTF-8" >
		<title> Tomcat 2</title>
	</head>

	<body>
		<h1> Hello from Tomcat 2</h1>
	</body>
</html>
```

### 5.编写启动脚本(startup.bat)和停止脚本（shutdown.bat）
		5.1 在tomcat7-multi-ins根目录新建tomcat1-startup.bat文件,作为ins-1的启动脚本，内容为：

```bat
@echo off

set CATALINA_BASE=D:\ProgramFiles\Java\tomcat7-multi-ins\ins-1

cd "%CATALINA_BASE%\bin"

set TITLE=Tomcat-1

call startup.bat %TITLE%
```

		5.2 在tomcat7-multi-ins根目录新建tomcat1-shutdown.bat文件，作为ins-1的停止脚本，内容为：
        
```bat
@echo off

set CATALINA_BASE=D:\ProgramFiles\Java\tomcat7-multi-ins\ins-1

cd "%CATALINA_BASE%\bin"

call shutdown.bat %TITLE%
```

		5.3 拷贝tomcat1-startup.bat为tomcat2-startup.bat，并修改其中的值：
```bat
@echo off

set CATALINA_BASE=D:\ProgramFiles\Java\tomcat7-multi-ins\ins-2

cd "%CATALINA_BASE%\bin"

set TITLE=Tomcat-2

call startup.bat %TITLE%
```

		5.5 拷贝tomcat1-shutdown.bat为tomcat2-shutdown.bat，并修改其中的值：
        
```bat
@echo off

set CATALINA_BASE=D:\ProgramFiles\Java\tomcat7-multi-ins\ins-2

cd "%CATALINA_BASE%\bin"

call shutdown.bat %TITLE%
```

### 6. 启动并测试
		6.1 分别执行tomcat1-startup.bat和tomcat2-startup.bat并访问 http://localhost:8080/test/和http://localhost:8081/test/ 确保启动成功

Reference:https://www.youtube.com/watch?v=eJjzD8tCwFI
