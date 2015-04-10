---
published: true
title: 使用jenkins实现自动化部署
layout: post
author: 可里胡 
category: 技术
tags:
- jenkins
- 持续集成
---

尽管团队规模非常少，甚至只有2、3条抢，我依然认为一个持续集成的环境对团队的开发效率及质量的把控是非常之有必要的，而且利用持续集成的自动化脚本，可以解决部署上线带来的诸多问题。使用持续集成的标准环境是：版本控制工具（如SVN、GIT），依赖包管理的Repository（如Maven），持续集成服务（如Jenkins），问题追踪工具（如redmine）。

###安装准备

[Jenkins](https://jenkins-ci.org/)提供了war包可以直接丢到tomcat容器里跑，如果你的项目比较小，可以使用jenkins的云服务，比如[travis-ci.org](http://travis-ci.org)。首次启动后，需要配置环境变量，如JDK、Maven、SVN或GIT账号等。

为了解决package部署的问题，比如Jenkins编译的版本要及时发布到测试机、准生产机上，这时可以通过编写shell脚本来完成，可以避免人工去拷贝文件、重启tomcat等。

为了方便脚本获取build完成的war包，先配置一个固定存档的路径，如：

<img src="http://pic.yupoo.com/hzg139/Ez5wum6u/medium.jpg" alt="QQ20150409-1@2x" width="500" height="126" border="0" />

这样每次build完成后，会得到一个存档的war包：

<code>http://192.168.3.30:8080/jenkins/lastSuccessfulBuild/artifact/joysim-hcb-root/joysim-hcb-vshop/target/test.war</code>

###部署脚本编写
编写服务器端的shell，用来自动部署build完成的war包，过程如下：

+	使用lsof命令查找tomcat进程并kill掉
+	从jenkins服务器上download war包并拷贝到tomcat/webapps下
+	启动tomcat

<pre><code>
#!/bin/bash
 
export JAVA_HOME=/usr/java/jdk1.6.0_45

#停止tomcat进程 
tomcat_pid=`/usr/sbin/lsof -n -P -t -i :8081`
[ -n "$tomcat_pid" ] && kill -9 $tomcat_pid
 
cd /opt/bak/
#rm  wp.war
rm -rf wp

#下载新的war包，并替换旧的
wget http://192.168.3.30:8080/jenkins/job/haochebang/lastSuccessfulBuild/artifact/joysim-hcb-root/joysim-hcb-wxweb/target/wp.war
rm /opt/webapp-t2/wp.war
rm -fr /opt/webapp-t2/wp

#替换环境配置文件.
unzip -n wp.war -d /opt/bak/wp
cp config/sys_conf.properties wp/WEB-INF/classes/
cp config/quartz.properties wp/WEB-INF/classes/
cp config/database.properties wp/WEB-INF/classes/jdbc/
cp config/applicationContext-base.xml wp/WEB-INF/classes/spring/

cp -r wp /opt/webapp-t2/


cd /opt/tomcat7-t2/bin/
./startup.sh
</code></pre>

将以上脚本创建为一个autoploy.sh的文件，并提交到仓库中。

###建立自动部署的Jenkins Job

接下来需要建立一个Jenkins Job专门做部署工作，创建一个freestyle的Job，配置Shell脚本项，使用远程命令执行shell脚本文件：

<img src="http://pic.yupoo.com/hzg139/EzdhI683/medium.jpg" alt="QQ20150410-3@2x" width="500" height="107" border="0" />

然后需要在build Job中，配置build完成后的后置项，把自动部署的job添加上去：

<img src="http://pic.yupoo.com/hzg139/EzdlRckN/medium.jpg" alt="QQ20150410-4@2x" width="500" height="252" border="0" />

OK，基于jenkins的简单自动部署方案就完成了，当然可以通过修改脚本来达到不同环境下的灵活自动部署。

