# 往 build gradle 添加findbugs 插件

#### findbugs api [连接]( "https://docs.gradle.org/current/dsl/org.gradle.api.plugins.quality.FindBugsExtension.html")

### 1. 找到build.gradle 主入口，因为是对所有子项目都做findbugs 检查，所以需要在 subprojects 属性里添加 findbugs
![](http://ww4.sinaimg.cn/large/7a8eda62gw1f55088wvz5j20bb04ajsx.jpg)

### 2. 之后要配置 findbugs 属性
```a. 需要指定html格式输出 ```
![](http://ww2.sinaimg.cn/large/7a8eda62gw1f550pdp5f9j20h40an0v9.jpg)
```b. 需要指定输出的路径```
![](http://ww1.sinaimg.cn/large/7a8eda62gw1f550oxaaxqj20h40axdin.jpg)

具体查看 [findbugs ext api](https://docs.gradle.org/current/dsl/org.gradle.api.plugins.quality.FindBugsExtension.html)

```findbugs{[属性]=[值]}```

而对于 [findbugs api](https://docs.gradle.org/current/dsl/org.gradle.api.plugins.quality.FindBugs.html)

![](http://ww1.sinaimg.cn/large/7a8eda62gw1f5510epm0mj20rl0j210b.jpg)

### 3.配置 repo

当启动build的时候 gradle会去repo定好的路径下寻找 findbugs jar包，如果没有才会去 maven repo上找，再download下来，本地缓存起来
![](http://ww3.sinaimg.cn/large/7a8eda62gw1f551anyff7j209m065q3g.jpg)

### 4.执行过程和结果
![](http://ww3.sinaimg.cn/large/7a8eda62gw1f551ck4w8uj20x30hewnf.jpg)
![](http://ww3.sinaimg.cn/large/7a8eda62gw1f551d36d3rj20lg04k0tv.jpg)