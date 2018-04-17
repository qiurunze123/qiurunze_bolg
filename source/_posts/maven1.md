---
title: maven jar 冲突解决
date: 2015-10-14 16:42:20
tags: maven
reward: true
---


### 冲突原因

A依赖于B及C，而B又依赖于X、Y，而C依赖于X、M，则A除引B及C的依赖包下，还会引入X，Y，M的依赖包（一般情况下了，Maven可通过<scope>等若干种方式控制传递依赖）。
这里有一个需要特别注意的，即B和C同时依赖于X，假设B依赖于X的1.0版本，而C依赖于X的2.0版本，A究竟依赖于X的1.0还是2.0版本呢？
这就看Classloader的加载顺序了，假设Classloader先加载X_1.0，而它就不会再加载X_2.0了，如果A恰恰希望使用X_2.0呢，血案就这样不期而遇了。

<!--more-->

###三个方法找出问题：
 1. mvn dependency:tree -Dverbose -Dincludes=asm:asm 
   [INFO] --- maven-dependency-plugin:2.1:tree (default-cli) @ ridge-test ---
[INFO] com.ridge:ridge-test:jar:1.0.2-SNAPSHOT
[INFO] +- asm:asm:jar:3.2:compile
[INFO] \- org.unitils:unitils-dbmaintainer:jar:3.3:compile
[INFO]    \- org.hibernate:hibernate:jar:3.2.5.ga:compile
[INFO]       +- cglib:cglib:jar:2.1_3:compile
[INFO]       |  \- (asm:asm:jar:1.5.3:compile - omitted for conflict with 3.2)
[INFO]       \- (asm:asm:jar:1.5.3:compile - omitted for conflict with 3.2)
[INFO] ------------------------------------------------------------------------

对asm有依赖有一个直接的依赖(asm:asm:jar:3.2)还有一个传递进入的依赖(asm:asm:jar:1.5.3)

2.将不想要的传递依赖剪除掉

承上，假设我们不希望asm:asm:jar:1.5.3出现，根据分析，我们知道它是经由org.unitils:unitils-dbmaintainer:jar:3.3引入的，那么在pom.xml中找到这个依赖，做其它的调整：

    <dependency>  
        <groupId>org.unitils</groupId>  
        <artifactId>unitils-dbmaintainer</artifactId>  
        <version>${unitils.version}</version>  
        <exclusions>  
            <exclusion>  
                <artifactId>dbunit</artifactId>  
                <groupId>org.dbunit</groupId>  
            </exclusion>  
            <!-- 这个就是我们要加的片断 -->  
            <exclusion>  
                <artifactId>asm</artifactId>  
                <groupId>asm</groupId>  
            </exclusion>  
        </exclusions>  
    </dependency>  


再分析一下，你可以看到传递依赖没有了：


    [INFO]  
    [INFO] --- maven-dependency-plugin:2.1:tree (default-cli) @ ridge-test ---  
    [INFO] com.ridge:ridge-test:jar:1.0.2-SNAPSHOT  
    [INFO] \- asm:asm:jar:3.2:compile  
    [INFO] ------------------------------------------------------------------------  
    [INFO] BUILD SUCCESS


3.要是还不好使就 清一下缓存哈哈