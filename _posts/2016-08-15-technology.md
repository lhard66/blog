## git原代码管理（站在项目管理角度）
### 一、大纲
* 介绍和说明
* 服务器分类和部署方法
* Bonobo系统的搭建
* 团队原代码管理方式
### 二、介绍和说明
　　这篇文章我会重点介绍git的`服务器端部署过程`和`团队协同开发原代码管理方式`。很多新手经常会问，问题：snv和git那个好，有什么区别？在回答之前，我先说一下我使用版本控制系统的历程。首先我也是从svn开始用起的，后来我们团队逐步改用git，但依然以svn为主。就个人来说，我几乎已完全转入git作为自己的版本控制工具。  
　　实在是忍不了了,我就来批判一下svn的种种让人抓狂的地方。:(  
　　1.svn需要经常连接服务器，服务器挂了就可以回家睡觉了。无论是检出项目、查看日志、合并文件、提交改动都需要连接服务器，而且工作的速度直接受制于服务器的快慢。您是否经历过下班后急着赶班车，但svn服务器半天反映的尴尬吗？  
　　2.日志文件会出现无法正常显示的问题。总是有同事问我，你提交了日志为什么不写日志信息？啊，怎么可能没有写？Hook Script程序是我设置的，我真的是太清楚了。如果没有写日志或日志字数未大于十个字节，提交是不会成功的。这个svn的bug让我难以忍受，还会影响到文件的打包合并工作。  
　　3.svn合并问题多多，痛苦不堪。我目前负责项目主干的合并工作，使用的版本控制系统就是svn。合并时会出现看不到日志信息而无法合并对应分支的版本、产生代码重复、注释行合并出错等等问题。这也是我开始讨厌使用svn的最大原因。想想git的强大的分支功能和行云流水般的合并快感，一个字：爽！爽！爽！  
　　总结：我对新手的回答只有一句话，snv相对于git真的弱爆了，只有弱者才使用svn。svn最大的优点可能就是简单易用，适合新手吧。  
### 三、服务器分类和部署方法
　　服务器部分我把它分为Windows Service平台和Linux平台。因为git开发者即是Linux的开发者，故git天生就很好的适应Linux平台,而对windows平台的支持并不太好。不过近几年随着git的普及和github（12年Andreessen Horowitz投资一亿美金）的发展壮大，git对windows平台的支持也日渐完善，但还是会有中文乱码等问题。因此，逼我使用英文写日志内容。
　　windows平台部署使用Bonobo系统，此系统是采用ASP.NET MVC开发并且开源，你可以随意订制和二次开发。适合小型开发团队的使用。
　　Linux就搭建更为复杂一些，功能也更为强大。主要有三种流行的方案：
　　1.Gitosis，轻量级，开源项目，只能做到库级的权限控制。
　　2.Gitoliste，轻量级，开源项目，能做到分支级的权限控制。可以满足大部分公司和企业的开发需求。
　　3.Git+Repo+Gerrit,重量级，集版本控制、仓库管理、代码审核为一身，可管理大型及超大型项目。大名鼎鼎的Android平台就是使用的此方案。
　　关于Linux部分的部署方法，这里不做介绍，可以自行谷歌。这里附上一篇文章：[《git服务器搭建及gitolite权限管理》](http://blog.csdn.net/redstarofsleep/article/details/45092135)  
### 四、Bonobo系统的搭建
　　1.需要.net framewrork、MVC，把Bobobo整个文件夹部署到~/inetpub/wwwwroot下面即可，位置也可自定义。  
　　2.打开IIS，配置应用程序，绑定IP地址等等。近似于部署网站。  
　　备注：[部署参考连接及下载地址](https://bonobogitserver.com/install/)  
　　3.**注意：**如果找不到IIS_USERS用户，有可能是刚开启IIS功能，重启一下电脑即可；由于这个asp.net mvc4项目里已经包含了git相关的功能，所以电脑上不需要另外再安装Git For Windows工具了。
### 五、团队原代码管理方式
　　**直接使用案例来讲解**  
　　**说明：**共有两个仓库：jim仓库、zgw仓库  
　　**涉及分支：**  
　　　　master  
　　　　maint  
　　　　teamjim  
　　　　origin/master  
　　　　origin/maint  
　　　　origin/teamjim  
　　　　jim/master  
　　**步骤：**  
　　　　git remote add jim http://192.168.0.167:8011/Bonobo.Git.Server.v5.2/jim.git  
　　　　git remote add origin http://192.168.0.167:8011/Bonobo.Git.Server.v5.2/zgw.git  
　　　　git checkout teamjim  
　　　　git fetch jim  
　　**此时本地jim/master分支文件已和jim成员分支同步**  
　　　　git merge jim/master	//合并jim/master分支内容到teamjim分支  
　　　　git checkout maint  
　　　　git merge teamjim		//将teamjim分支内容合并到maint用于测试问题  
　　　　git merge master maint		//将maint中的内容合并到master主干上  
　　　　git push --all origin		//推送主干和所有分支的内容  
