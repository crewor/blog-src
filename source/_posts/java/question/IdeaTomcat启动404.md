---
title: idea Tomcat 项目启动成功，访问404
keywords: idea Tomcat 启动成功 访问404
date: 2018-12-11 12:00:00
---
### idea Tomcat 项目启动成功，访问404

<!--more-->
###
    idea Tomcat 项目启动成功，访问404
    可以确认Tomcat启动成功，但是项目初始化失败，查看Tomcat启动日志发现错误
    2018-12-11 14:51:26 org.apache.catalina.core.StandardContext loadOnStartup
    严重: Servlet  threw load() exception
    java.lang.NoSuchMethodError: org.springframework.core.GenericTypeResolver.resolveTypeArguments(Ljava/lang/Class;Ljava/lang/Class;)[Ljava/lang/Class;
    	at org.springframework.core.convert.support.GenericConversionService.getRequiredTypeInfo(GenericConversionService.java:275)
    	at org.springframework.core.convert.support.GenericConversionService.addConverter(GenericConversionService.java:93)
    	at org.springframework.core.convert.support.DefaultConversionService.addScalarConverters(DefaultConversionService.java:61)
    	at org.springframework.core.convert.support.DefaultConversionService.addDefaultConverters(DefaultConversionService.java:53)
    	at org.springframework.core.convert.support.DefaultConversionService.<init>(DefaultConversionService.java:42)
    	at org.springframework.core.env.AbstractPropertyResolver.<init>(AbstractPropertyResolver.java:44)
    	at org.springframework.core.env.PropertySourcesPropertyResolver.<init>(PropertySourcesPropertyResolver.java:42)
    可以定位到错误是spring没有方法resolveTypeArguments(Ljava/lang/Class;Ljava/lang/Class;)，从这里可以想到是jar包冲突了
    这里就可以排查新引入的jar包是否有引入spring包，导致的jar包冲突，然后将spring包加入exclusion