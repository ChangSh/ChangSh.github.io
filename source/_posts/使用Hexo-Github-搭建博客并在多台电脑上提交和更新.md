---
title: 使用Hexo + Github 搭建博客并在多台电脑上提交和更新
date: 2018-08-07 01:01:35
tags:
---
### 使用Hexo + Github 搭建博客并在多台电脑上提交和更新
**注意：**
**1、既然看到了这篇文章（尚且不清楚github pages的seo做的怎么样，不过不知道的通通按标题匹配进来的处理），我敢断言你一定和我一样，不适应当前所有技术博客网站的简（la）洁（ji）UI，不管你是一个一无所知的萌新，还是遇到了多机更新困惑的大鸟，希望你能在此找到你想要的答案。**
**2、需要特别注意的地方，都用**
>example

**来标识** 


***OS：windows 10***
**相关步骤**
* 安装Node.js和配置Node.js环境，打开cmd命令行，成功界面如下
TODO 插图
* 安装Git和配置好Git环境，安装成功则在电脑任何位置鼠标右键可以看到如下两个选择
TODO 插图
注意：一般出于安全考虑，只有在Git Bash Here中才能进行Git的相关操作。如果需要在cmd命令行里调用Git，那么就要配置电脑的环境变量Path，或者在安装的时候选择use Git from the Windows Command Prompt。这个可有可无，影响不大，成功配置的界面如图
TODO 插图
* Github账户注册和新建仓库，仓库格式要遵守 ***账户名.github.io*** ,勾选Initialize this repository with a README
TODO 插图

* 安装Hexo，在合适的地方创建文件夹，我在D盘创建了BLOG文件夹，右键Git Bash Here
  输入npm install hexo -g，开始安装Hexo
  TODO 插图
  输入hexo -v ，检查hexo是否安装成功
  TODO 插图
 * 输入hexo init，初始化该文件夹
  TODO 插图
  > 到这里，生成的所有文件
	
  
  
