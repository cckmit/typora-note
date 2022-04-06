# linux 搭建服务环境

```shell
#查看所有进程
netstat   -nultp
#查看所有java进程明细
ps -ef|grep java
ps grep|gerp java
#杀死指定进程
kill -9 -port

```

## 1.java环境

```shell
1.下载 JDK
进入 Oracle 官方网站 下载合适的 JDK 版本，准备安装。
注意：这里需要下载 Linux 版本。这里以jdk-8u151-linux-x64.tar.gz为例，你下载的文件可能不是这个版本，这没关系，只要后缀(.tar.gz)一致即可。

2. 创建目录
在/usr/目录下创建java目录，

mkdir /usr/java
cd /usr/java
把下载的文件 jdk-8u151-linux-x64.tar.gz 放在/usr/java/目录下。

3. 解压 JDK
tar -zxvf jdk-8u151-linux-x64.tar.gz
4. 设置环境变量
修改 /etc/profile
在 profile 文件中添加如下内容并保存：

配置环境变量。输入命令：
vim /etc/profile
·用文本编辑器打开/etc/profile
·在profile文件末尾加入：
export JAVA_HOME=/usr/share/jdk1.6.0_14
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

让修改生效：
source /etc/profile
5. 测试
java -version
```

## 2.mysql 环境

```shell
1.查看虚拟机内是否有mysql，如果有，则需要删除mysql相关文件
rpm -qa|grep mysql
rpm -ev xxx --nodeps
2.在线安装数据库
install mysql-server
3.启动数据库服务
service mysqld start
4.利用临时密码登录到Mysql客户端
sudo mysql -u root -p 
5.设置新的Mysql密码（不再使用临时密码）
alter user 'root'@'localhost' identified by '@wjb13191835106'; --by 后面为新密码
6. 【可选操作，用于设置复杂密码】修改validate_password_policy参数值为0（1为开启复杂策略）
set global validate_password_policy=0;（默认为1）
set global validate_password_length=1;
7.授权允许远程访问
grant all privileges on *.* to 'root'@'%' identified by '@wjb13191835106';
注意：请把命令中的【@wjb13191835106】更改为自己的Mysql密码。
刷新授权：此时，你的Mysql就可以被远程连接了。
flush privileges; 
关闭授权
revoke all on *.* from dba@localhost;
8.设置开机启动
编辑rc.local文件
vi /etc/rc.local
在rc.local文件尾部添加以下代码：
mkdir -p /var/run/mysqld
chown mysql.mysql /var/run/mysqld/
设置rc.local权限
chmod 774 /etc/rc.d/rc.local
```

## 3.tomcat环境

```shell
1.下载并加压tomcat的linux版本文件（省略）
2.配置环境变量
vim /etc/profile
3.在profile文件末尾加入
export TOMCAT_HOME=/home/pan/tomcat/apache-tomcat-8.0.23
export CATALINA_HOME=/home/pan/tomcat/apache-tomcat-8.0.23 --tomcat文件路径
4.设置环境变量立即生效
source /etc/profile
5.进入/usr/local/tomcat/bin目录
cd /usr/local/tomcat/bin
6.启动tomcat
./startup.sh
7.关闭tomcat
./shutdown.sh
8.设置端口，进入conf文件，默认为8080
cd /home/pan/tomcat/apache-tomcat-8.0.23/conf
修改xml文件下的端口，保存退出  :wq
vim server.xml
```

## 4.Redis环境

```shell
1.安装gcc环境 由于redis是由C语言编写的，它的运行需要C环境，因此我们需要先安装gcc。安装命令如下：
yum install gcc-c++
2.下载并加压redis的linux版本文件
3.cd 切换到redis-4.0.1目录下进行编译
make
4.编译完成以后进行安装，默认安装安装/usr/local/bin
make install
5.执行命令启动redis服务
./redis-server 
6.、设置后台启动redis
            1）、首先编辑conf文件，将daemonize属性改为yes（表明需要在后台运行）
                   cd etc/
                   vim redis.conf
                     将no修改为yes
            2)、再次启动redis服务，并指定启动服务配置文件
                  redis-server /usr/local/redis/etc/redis.conf
7.启动 redis
service redis start
8.设置redis 开机启动
chkconfig redis on
```

