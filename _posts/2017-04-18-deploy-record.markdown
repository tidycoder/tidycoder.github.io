
---
layout:     post
title:      "deploy process!"
subtitle:   ""
date:       2017-03-07 12:00:00
header-img: "img/post-bg-2015.jpg"
tags:
    - 运维
---

### 机器配置说明

lkl.data: xx.xx.xxx.xxx(公) yy.yy.yyy.yy(内)
lkl.biz: aaa.aa.aa.aa(公) bb.bb.bbb.bbb(内)

两台机器：lkl.biz用于跑逻辑服，包涵tomcat, nginx, php.
lkl.data跑数据相关基础设施, 包含mysql, redis, metaq, zookeeper.

修改本地/etc/hosts. 增加
xx.xx.xxx.xxx  lkl.data
aaa.aa.aa.aa   lkl.biz

 修改lkl.biz上/etc/hosts. 增加
yy.yy.yyy.yy lkl.data



### java 的安装。 机器：lkl.data和lkl.biz
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u60-b27/jre-8u60-linux-x64.rpm"
sudo yum localinstall jre-8u60-linux-x64.rpm
rm ~/jre-8u60-linux-x64.rpm

### redis 集群安装。机器lkl.biz

安装redis
wget http://download.redis.io/releases/redis-3.2.8.tar.gz
tar xzf redis-3.2.8.tar.gz
cd redis-3.2.8
make
mv redis-3.2.8 /opt/
集群启动
yum install ruby rubygems
gem install redis
cd redis-3.2.8/utils/create-cluster
./create-cluster start
./create-cluster create

### mysql安装. 机器lkl.data

配置源：
wget dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm
yum localinstall mysql-community-release-el6-5.noarch.rpm
yum repolist all | grep mysql
yum-config-manager --disable mysql55-community
yum-config-manager --disable mysql56-community
yum-config-manager --enable mysql57-community-dmr
yum repolist enabled | grep mysql
安装：
yum install mysql-community-server
启动：
service mysqld start
设置开机启动：
chkconfig --list | grep mysqld
chkconfig mysqld on
修改/etc/my.cnf, 增加编码:
[mysqld]
character-set-server=utf8

拷贝lkl的sql到该机器。
生成educaton表并创建lkl专属用户

使用root登陆并设置密码：
mysql -u root < education_schema.sql
mysql -u root < user.sql



### metaq 和 zookeeper的安装. 机器lkl.data

安装并运行zookeeper
wget http://apache.claz.org/zookeeper/zookeeper-3.3.6/zookeeper-3.3.6.tar.gz
tar zxvf zookeeper-3.3.6.tar.gz
mv zookeeper-3.3.6 /opt/
cd  /opt/zookeeper-3.4.6
mkdir data
cp conf/zoo_sample.cfg  conf/zoo.cfg
修改conf/zoo.cfg:
dataDir=/opt/zookeeper-3.3.6/data
maxClientCnxns=30
bin/zkServer.sh start

安装并运行metaq
wget https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/meta-queue/metaq-server-1.4.6.2.tar.gz
tar zxvf metaq-server-1.4.6.2.tar.gz
mv taobao /opt/
cd /opt/taobao
cp -rf  metamorphosis-server-wrapper ../metaq9123
bin/metaServer.sh start


### nginx php-fpm. 机器lkl.biz上

yum -y install nginx
/etc/init.d/nginx start

yum install php php-fpm php-devel php-bcmatch php-gd php-mbstring php-mcrypt php-mysql
service php-fpm start

使用到nginx和php的主要三部分： 1. upload 2. pic 3. community(bbs)。
创建用户www www.修改php和nginx的用户为www www.
拷贝lkl-bbs拷贝到/home/wwwroot下。并配置nginx。 

注意事项：
php.ini中增加：
upload_tmp_dir = /tmp
upload_max_filesize = 20M
nginx.conf中增加:
client_body_temp_path /tmp/nginx-client-body;
proxy_temp_path /tmp/nginx-proxy;
fastcgi_temp_path /tmp/nginx-fastcgi;
uwsgi_temp_path /tmp/nginx-uwsgi;
scgi_temp_path /tmp/nginx-scgi;


配置细节略。配置可参考lkl.biz上的已有配置。

### tomcat 机器lkl.biz上

wget http://ftp.wayne.edu/apache/tomcat/tomcat-7/v7.0.77/bin/apache-tomcat-7.0.77.tar.gz
tar zxvf apache-tomcat-7.0.77.tar.gz
sudo mv apache-tomcat-7.0.77 /usr/local/tomcat
cd /usr/local/tomat
bin/startup.sh
将war文件拷贝到目录webapps下进行部署


### solr 安装部署 lkl.data
wget http://archive.apache.org/dist/lucene/solr/5.3.1/solr-5.3.1.zip
unzip solr-5.3.1.zip
./install_solr_service.sh ../../solr-5.3.1.zip -d /var/solr -i /opt -p 8983
service solr start

在Web管理页面新建eduTeacher core. 需要的操作包括：
1.webapp中添加必要的lib
2.webapp下WEB-INF/web.xml添加：
	<listener>
    <listener-class>org.apache.solr.handler.dataimport.scheduler.ApplicationListener</listener-class>
  </listener>
3.在solrhome目录下新建一个conf文件夹，将dataimport.properties文件放在conf文件夹中。
4.sudo service solr restart


### 安装lib到internal private repo. 
说明：此步骤只需执行一次，下次无需部署。
下载nexus，并安装到ops.uuke.co. 有些目前已经找不到的依赖库，通过上传到自己本地的私有仓库中解决。比如:
mvn deploy:deploy-file -DgroupId="net.sf.log4jdbc" -DartifactId="log4jdbc4" -Dversion="1.2.1" -Dpackaging="jar" -Dfile="./log4jdbc4-1.2.1.jar" -DrepositoryId="maven-releases" -Durl="http://ops.uuke.co:8081/repository/maven-releases/"

