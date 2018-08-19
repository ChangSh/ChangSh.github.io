---
title: 开发时候maven jar包依赖的一些注意事项
date: 2018-08-19 22:32:51
tags:
---

#### maven 生命周期

生命周期（lifecycle）由各个阶段组成，每个阶段由maven的插件plugin来执行完成。


我们如配置了maven-compiler-plugin这个插件，其它的插件没有配置，但最后项目构建成功，说明maven内置的各种插件，如果pom中没有配置就调用默认的内置插件，如果pom中配置了就调用配置的插件。

我们理解maven的构建过程或者有更多的人称是打包，就是由各种插件按照一定的顺序执行来完成项目的编译，单元测试、打包、布署的完成。各种插件的执行过程也就构成的maven的生命周期（lifecycle）。

生命周期（lifecycle）各个阶段并不是独立的，可以单独执行如mvn clean，也可以一起执行如mvn clean install。而且有的mvn命令其是包括多个阶段的，如mvn compile其是包括了resources和compile两个阶段。
　

* mvn clean package依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)等7个阶段。
* mvn clean install依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)、install等8个阶段。
* mvn clean deploy依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)、install、deploy等9个阶段。

它们的主要区别如下，

* package命令完成了项目编译、单元测试、打包功能，但没有把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库和远程maven私服仓库
* install命令完成了项目编译、单元测试、打包功能，同时把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库，但没有布署到远程maven私服仓库
* deploy命令完成了项目编译、单元测试、打包功能，同时把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库和远程maven私服仓库　　

#### **持续集成** 时开发环境，测试环境，线上环境切换时的注意事项：

* 利用profile来切换环境 
```
<activation>
     <activeByDefault>true</activeByDefault>
</activation>
```

* 开发环境
    1. 由于开发环境是直接从master上面拉的分支，因此pom的有关配置都是和master主干（线上环境）一致，尤其是版本号，如 1.9.26-RELEASE。
    2. admin项目依赖item项目，admin项目的pom文件中依赖item项目jar包的版本号和item项目本身pom文件中的版本号一致（都是1.9.26-RELEASE）。
    3. item项目开发完成后执行 mvn install 命令，admin项目的maven插件自动检测依赖的变化，reimport检测到maven 根据版本号打包，因此本地仓库中的1.9.26-RELEASE jar包文件更新。
    4.  主干的profile的active状态是dev，只好可以在拉开发分之后对应开发配置。
* 测试环境
    1. 测试环境是独立的测试分支test，profiles的active为test。
    2. item项目所有的turbo接口都是1.2.5-SNAPSHOT，admin项目的测试环境中对item项目依赖的版本也是1.2.5-SNAPSHOT快照版本，这样，**有一点需要注意的地方：** 从自己的开发分支merge到test分支之前，如果不把自己分支的版本号改成1.2.5-SNAPSHOT，test分支的pom文件版本号会被自己分支的版本号（1.9.26-RELEASE）覆盖。
    3. 为了避免出现因为忘记导致第二项版本被覆盖的情况，应该在拉完开发分支后直接修改版本号为1.2.5-SNAPSHOT。
* 线上环境
 TODO

测试环境的打包脚本
```
#!/usr/bin/env bash

DIR=$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )

cd ${DIR}
mvn -e versions:set \
    -DgroupId=cn.com.autohome.mall \
    -DartifactId=mall-api-aggregator \
    -DnewVersion=1.2.5-SNAPSHOT
mvn -e clean compile deploy -P release-sign-artifacts -DskipTests
```
执行结果如下图：
![images](http://182.61.41.64/images/1534499441674.png)

脚本执行了 **mvn -e clean compile deploy** 

在持续集成服务器上使用怎样的 mvn 命令集成项目，下面是一些总结：

* 不要忘了clean： clean能够保证上一次构建的输出不会影响到本次构建。
* 使用deploy而不是install：构建的SNAPSHOT输出应当被自动部署到私有Maven仓库供他人使用。
* 使用-U 参数： 该参数能强制让Maven检查所有SNAPSHOT依赖更新，确保集成基于最新的状态，如果没有该参数，Maven默认以天为单位检查更新，而持续集成的频率应该比这高很多。
使用-e参数：如果构建出现异常，该参数能让Maven打印完整的stack trace，以方便分析错误原因。
使用-Dmaven.repo.local参数：如果持续集成服务器有很多任务，每个任务都会使用本地仓库，下载依赖至本地仓库，为了避免这种多线程使用本地仓库可能会引起的冲突，可以使用-Dmaven.repo.local=/home/juven/ci/foo-repo/这样的参数为每个任务分配本地仓库。
使用-B参数：该参数表示让Maven使用批处理模式构建项目，能够避免一些需要人工参与交互而造成的挂起状态。

综上，持续集成服务器上的集成命令应该为 mvn clean deploy -B -e -U -Dmaven.repo.local=xxx 。
测试打包直接用运行脚本
deploy.sh
默认测试快照版本就是 1.2.5-SNAPSHOT


