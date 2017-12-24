---
title : "resin 配置"
date: 2017-06-17 19:42:25
tags: ["linux"]
categories: ["linux"]
---

# resin 配置
## resin 配置日志信息
```xml
<log-handler name="" level="info" path="stdout:"
               timestamp="[%y-%m-%d %H:%M:%S.%s] {%{thread}} "/>
<stdout-log path-format="log/stdout.log.%Y%m%d" rollover-period="1D"/>
<stderr-log path-format="log/stderr.log.%Y%m%d" rollover-period="1D"/>

<!--
 - level='info' for production
 - 'fine' or 'finer' for development and troubleshooting
-->
<logger name="com.caucho" level="info"/>

<logger name="com.caucho.java" level="config"/>
<logger name="com.caucho.loader" level="config"/>
```
`stdout-log`中的`path-format`设置了正常输出日志的路径和日志文件命名格式；`rollover-period`设置了日志文件生成时间间隔。 `1D`是一天，`1H`是一个小时。
<!-- more -->
```
<cluster id="app">
    <!-- sets the content root for the cluster, relative to resin.root -->
    <root-directory>.</root-directory>

    <!-- defaults for each server, i.e. JVM -->
    <server-default>
      <!-- The http port -->
      <http id="" address="*" port="9090"/>
    </server-default>

    <!-- define the servers in the cluster -->
    <server id="web" address="127.0.0.1" port="6801"/>

    <!-- the default host, matching any host name -->
    <host id="" root-directory=".">
      <!--
         - configures an explicit root web-app matching the
         - webapp's ROOT
        -->
	<web-app id="/" root-directory="/search/odin/resin/umiswxb"/>      
      <!--
         - Administration application /resin-admin
        -->
      <!--
      <web-app id="/resin-admin" root-directory="${resin.root}/doc/admin">
        <prologue>
          <resin:set var="resin_admin_external" value="false"/>
          <resin:set var="resin_admin_insecure" value="true"/>
        </prologue>
      </web-app>
        -->
      <!--
	 - Resin documentation - remove for a live site
	-->
      <!--<web-app id="/resin-doc" root-directory="${resin.root}/doc/resin-doc"/>-->

      <!--
	 - <resin:LoadBalance regexp="^/load" cluster="backend-tier"/>
	 - <resin:HttpProxy regexp="^/http" address="localhost:9000"/>
	 - <resin:FastCgiProxy regexp="^/fcgi" address="localhost:9001"/>
	-->
    </host>
  </cluster>
```
`cluster`可以配置不同的服务，`id`属性是其服务唯一的标记
`server-default`中的`http`的`port`配置了该服务监听的端口，`web-app`的`id`属性定义了服务的访问路径，`root-directory`定义了服务代码放置的位置。

## 配置远程调试端口

在 resin3.1下的版本中，修改 bin/httpd.sh 文件中的配置
```
args="-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8787"
```
在 resin3.1 以上的版本中，修改 conf/resin.properties 文件
```
jvm_args  :  -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=9090  
```
或者修改 resin.xml 文件，在 `server-default` 标签下，添加
```xml
<jvm-arg>-Xdebug</jvm-arg>
<jvm-arg>-Xrunjdwp:transport=dt_socket,address=9988,server=y,suspend=n</jvm-arg>
```
