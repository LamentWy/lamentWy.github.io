---
layout: post
title: mybatis generator
description: 如何使用MBG插件自动生成dao层代码
---

## What is Mybatis Generator

MyBatis Generator (MBG) 是一个Mybatis的代码生成器 MyBatis 和 iBATIS. 他可以生成Mybatis各个版本的代码，和iBATIS 2.2.0版本以后的代码。 他可以内省数据库的表（或多个表）然后生成可以用来访问（多个）表的基础对象。  
这样和数据库表进行交互时不需要创建对象和配置文件。
MBG的解决了对数据库操作有最大影响的一些简单的CRUD操作。   
您仍然需要对联合查询和存储过程手写SQL和对象。

## Mybatis Generator会生成什么？

> 仅列出了常用的部分

+ 与数据库表匹配的pojo类

+ Mybatis/ibatis兼容的sql映射文件（xxxmapper.xml）

其中会包括一些常见CRUD操作的sql语句，比如：  
insert , update by primary key , delete by primary key , select by primary key ,count by example ...

+ 还可以为Mybatis 3.x版本及以上生成客户端，比如:mapper接口类。

## Mybatis Generator 与 maven 集成.

> 为了简化书写，后面将Mybatis Generator 简写为 MBG。

在迭代式开发的环境中，可以将MBG作为一个maven插件集成进去。   
需要注意的是：   

> 当MBG新生成一个与已存在的xml文件同名的文件时，它会自动合并二者的内容，并且不会覆盖掉原有xml文件中你所编写的内容，仅覆盖原xml中属于MBG生成的部分。  

> MBG不会合并.java文件，只会覆盖或者另存为一个新的.java文件，当你不想覆盖掉原有.java文件时，需要自行手动合并。（某些eclipse的MBG插件支持自动合并）

## 运行MBG
1. 命令行
2. ant任务，使用xml配置文件。
3. 作为maven插件。

### 这里选择详细介绍作为maven插件运行MBG，因为手头所有项目均用maven构建，且方便切换ide

+ pom.xml，最简配置:  

{% highlight ruby %}
<project ...>
     ...
     <build>
       ...
       <plugins>
        ...
        <plugin>
      	  <groupId>org.mybatis.generator</groupId>
      	  <artifactId>mybatis-generator-maven-plugin</artifactId>
          <version>1.3.0</version>
        </plugin>
        ...
      </plugins>
      ...
    </build>
    ...
  </project>
{% endhighlight %}

+ Maven Goal & Execution

mvn命令行:

> mvn mybatis-generator:generate   

mvn build时自动执行:   

{% highlight ruby %}  

<project ...>
     ...
     <build>
       ...
       <plugins>
        ...
        <plugin>
      	  <groupId>org.mybatis.generator</groupId>
      	  <artifactId>mybatis-generator-maven-plugin</artifactId>
          <version>1.3.0</version>
          <executions>
            <execution>
              <id>Generate MyBatis Artifacts</id>
              <goals>
                <goal>generate</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
        ...
      </plugins>
      ...
    </build>
     ...
</project>

{% endhighlight %}  
 
> 会在编译之前执行   
