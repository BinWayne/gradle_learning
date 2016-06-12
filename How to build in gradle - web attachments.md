# 快速搭建gradle build

## eOrder 项目结构

gradle 本质其实是groovy 语言
<br/>gradle 文件里面的语法和groovy 几乎一样，一份gradle文件即 对应的是 gradle project对象，里面的属性都和gradle project api对应，
<br/>参考gradle 语法api [gradle api](https://docs.gradle.org/current/dsl/org.gradle.plugins.ear.Ear.html "gradle api")
###注
前提 必须先安装 gradle 以及配置好gradle 环境变量


## 1. build jar包
举例  build ‘Common Base’ project
<br/>
目录结构：
![Common Base 目录结构](http://ww3.sinaimg.cn/large/7a8eda62gw1f4esnhupusj20f8072jth.jpg)
Class Path
![](http://ww3.sinaimg.cn/large/7a8eda62gw1f4esoqwldtj20iv0bmq55.jpg)
Deployment Assembly
![](http://ww2.sinaimg.cn/large/7a8eda62gw1f4esrk0mzvj20t30fjgpc.jpg)

所以 common base项目依赖于

- ead4-common-bin.jar
- ead4j-jade-bin.jar
- ead4j-opal-bin.jar
- was-lib-path
- jdom.jar
- apache-common-lang.jar

接着创建 对应的 build gradle 文件， zjava_Common_Base.gradle
![](http://ww4.sinaimg.cn/large/7a8eda62gw1f4et29y4oej20fy0bqad4.jpg)

- ``apply plugin:'java'`` 意思是针对java项目进行编译
- ``project.archivesBaseName`` 意思是最终编译成jar包后的命名
- ``dependencies{}`` 里面放置一些依赖包
- ``sourceSets -> main -> java -srcDirs = 'com'`` 指明了源文件入口

![](http://ww3.sinaimg.cn/large/7a8eda62gw1f4et5ljdm4j20990710uj.jpg)

执行脚本

![](http://ww4.sinaimg.cn/large/7a8eda62gw1f4eykgbr4mj208x04amyi.jpg)

###注：每一个 UP-TO-DATE前执行的动作 都是 project内嵌的task

### 注意
``dependencies{}`` 中的 ``compileOnly files`` 意思是只有编译的时候运动，并不会被打入jar包。这跟后面打war包时 ``providedCompile files``意思差不多，后续会讲解``providedCompile files``，如果是``compile files``那么这些jar同时也被打入这个项目中。



## 2. build WAR 包
因为做的是web项目build，针对一些java项目最终都会打成jar包并被其他web项目依赖做成war包
<br/>
现在的所有的 gradle build 脚本都放在和 工程项目 <strong>平行</strong> 的项目文件夹里，如图
![](http://ww4.sinaimg.cn/large/7a8eda62gw1f4ex6ltxloj209o0jdaeg.jpg)

###注
如果仅仅 是对一个java 项目进行编译，那么默认的build 脚本文件名字叫 build.gradle

现在在WEB-EORDER-GRADLE-SCRIPTS中建立如下几个build 文件

- build.gradle
- gradle.properties
- settings.properties
- zwar_DSWWebAttachments.gradle
- zear_DSWWebAttachmentsEAR.gradle
<br/><strong>...</strong>
![](http://ww3.sinaimg.cn/large/7a8eda62gw1f4exaxlgqxj20au0it0yr.jpg)

###注
其实针对workspace里的每个web项目 我们都创建了对应的zwar_*, zwear_* ，而真正的入口 build 文件是 build.gradle
<br/>

ok，回到 DSWWebAttachments这个web项目，编译一个web项目

- 首先要分析有哪些引用的class path，
- 然后有哪些引用 project，
- 再看看 deployement assembly里面的jar
- 最后看WEB-INF/lib (这里面的jar是运行时提供，最终会被拷贝到war包中的web-inf/lib 下)

这三者决定了 我们在写 ```dependencies{}``` 时候确保所有的编译和运行所需的jar不缺
<br/>来看看这三者

java build path
![](http://ww4.sinaimg.cn/large/7a8eda62gw1f4exns4wrjj20j706edhf.jpg)
没有引用project

<br/>
deployment assembly
![](http://ww4.sinaimg.cn/large/7a8eda62gw1f4exour6nej20m00dyten.jpg)

再看我们已经写好的zwar_DSWWebAttachments.gradle
![](http://ww4.sinaimg.cn/large/7a8eda62gw1f4exj0t1wuj20j40jndlw.jpg)

先看上半部分 主体

- ```apply plugin:'war'``` //需要编译什么类型 就 申请什么类型
- ```archivesBaseName``` 即编译后生成 ```${}.war```
- ```webAppDirName``` 对应的WEB-INF 的父级入口se
- ```sourceSets```即源文件入口
- ```dependencies{}``` 编译和运行所依赖的jar

在```dependencies{}``` 中，providedCompile 和 compile 是有区别的，之前提过providedCompile 仅仅在编译java阶段提供，而在运行时候 不需要，而compile 出了编译阶段提供，最终也会被copy进WEB-INF/lib里去。

<br/>
另外 ``` task replaceEnvConfiguration << { }```可以随意指定 task，并且把 这个task 通过dependsOn放置在某个task之前

## 3. build.gradle

如图1. 定义subprojects 属性

![](http://ww2.sinaimg.cn/large/7a8eda62gw1f4eyydhg84j20hn0fo44c.jpg)

如图2 build jar

![](http://ww4.sinaimg.cn/large/7a8eda62gw1f4eyz89vvgj20ig0j87aw.jpg)

如图3 build war

![](http://ww4.sinaimg.cn/large/7a8eda62gw1f4ez7cxdoyj20fk0ahwhx.jpg)

如图4  build ear

![](http://ww3.sinaimg.cn/large/7a8eda62gw1f4ez1nfq3hj20iq096goj.jpg)

由于是扁平化管理build 脚本，每个项目和 脚本目录放在同一个根目录下，重点说下 build.gradle

### 首先它build 入口文件
所以的 项目编译入口都放在build.gradle里，包括build jar，build war， build ear

### 如何route

``` project(':${项目名}'){
	apply from: "${rootDir}/${对应项目的build jar/war/ear包}.gradle"```
	
```	dependencies{
		//需要写 之前引用的 项目
		compile project(':${之前在build.gradle定义的 build jar 名}')
	}
```

![](http://ww2.sinaimg.cn/large/7a8eda62gw1f4ezb66rvfj20xo0aa7c1.jpg)

![](http://ww2.sinaimg.cn/large/7a8eda62gw1f4ezcsegj8j20u70av108.jpg)

###执行脚本

![](http://ww2.sinaimg.cn/large/7a8eda62gw1f4ezf7h0uaj20hs0mhqe4.jpg)

## 3. build EAR包

###1. 创建 project module in build.gradle
![](http://ww4.sinaimg.cn/large/7a8eda62gw1f4ezjc81xwj20wv04gtbq.jpg)

###2.创建对应的 zear_DSWWebAttachmentsEAR.gradle
![](http://ww2.sinaimg.cn/large/7a8eda62gw1f4ezkq9dy7j20va06ptcp.jpg)

###3. 编写zear_DSWWebAttachmentsEAR.gradle
![](http://ww3.sinaimg.cn/large/7a8eda62gw1f4eztryo78j20qn0dhq7l.jpg)

###4. 在settings.gradle中配置对应的 project 名称
![](http://ww1.sinaimg.cn/large/7a8eda62gw1f4ezmyf9gxj20e40c777k.jpg)

 







