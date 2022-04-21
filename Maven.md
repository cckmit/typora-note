# Maven文档

## 1.Maven的下载
在Maven的官网即可下载，点击访问Apache Maven。


下载后解压即可，解压后目录结构如下：

## 2.Maven常用配置
在配置之前请将JDK安装好。

1. 环境变量配置
    添加M2_HOME:对应Maven的解压目录即可。


  编辑Path环境变量：

  测试，在cmd窗口输入mvn -v查看，显示版本如下即配置成功：

2. 修改配置文件
    通常我们需要修改解压目录下conf/settings.xml文件，这样可以更好的适合我们的使用。

（1）本地仓库位置修改 localRepository
在<localRepository>标签内添加自己的本地位置路径

```xml
<localRepository>D:\tools\repository</localRepository>
```

（2）修改maven默认的JDK版本
在<profiles>标签下添加一个<profile>标签，修改maven默认的JDK版本。

```xml
<profile>     
    <id>JDK-1.8</id>       
    <activation>       
        <activeByDefault>true</activeByDefault>       
        <jdk>1.8</jdk>       
    </activation>       
    <properties>       
        <maven.compiler.source>1.8</maven.compiler.source>       
        <maven.compiler.target>1.8</maven.compiler.target>       
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>       
    </properties>       
</profile>
```

（3）添加国内镜像源 mirror
添加<mirrors>标签下<mirror>，添加国内镜像源，这样下载jar包速度很快。默认的中央仓库有时候甚至连接不通。一般使用阿里云镜像库即可。这里我就都加上了，Maven会默认从这几个开始下载，没有的话就会去中央仓库了。

```xml
<!-- 阿里云仓库 -->
<mirror>
    <id>alimaven</id>
    <mirrorOf>central</mirrorOf>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
</mirror>

<!-- 中央仓库1 -->
<mirror>
    <id>repo1</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo1.maven.org/maven2/</url>
</mirror>

<!-- 中央仓库2 -->
<mirror>
    <id>repo2</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo2.maven.org/maven2/</url>
</mirror>
```

(4) 添加代理proxies

其下面可以定义一系列的proxy子元素，表示Maven在进行联网时需要使用到的代理。当设置了多个代理的时候第一个标记active为true的代理将会被使用。下面是一个使用代理的例子：

```xml
<proxies>
  <proxy>
      <id>xxx</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>用户名</username>
      <password>密码</password>
      <host>代理服务器地址</host>
      <port>代理服务器的端口</port>
      <nonProxyHosts>不使用代理的主机</nonProxyHosts>
  </proxy>
</proxies>
```

(5) 添加远程服务器

其下面可以定义一系列的server子元素，表示当需要连接到一个远程服务器的时候需要使用到的验证方式。这主要有username/password和privateKey/passphrase这两种方式。以下是一个使用servers的示例：

```xml
<servers>
    <server>
      <id>id</id>
      <username>用户名</username>
      <password>密码</password>
    </server>
  </servers>
```

## 3.Maven自动化部署

> （1）使用wagon-maven-plugin插件，在pom.xml中加入

```xml
<build>
        <!-- maven扩展 提供ssh远程服务 是wagon-maven-plugin插件所依赖 -->
        <extensions>
            <extension>
                <groupId>org.apache.maven.wagon</groupId>
                <artifactId>wagon-ssh</artifactId>
                <version>2.8</version>
            </extension>
        </extensions>
        <plugins>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>wagon-maven-plugin</artifactId>
                <version>2.0.2</version>
                <configuration>
                    <!-- 拷贝目录下(执行目录) target目录下的jar包 -->
                    <fromFile>target/${project.artifactId}-${project.version}.jar</fromFile>
                    <!-- 使用scp传输文件 指定服务端 用户名密码 ip 并指定目标文件夹-->
                    <url>scp://${user}:${password}@${ip}/usr/springboot/app</url>
                    <!-- 命令列表 可以在传输完成后执行 -->
                    <commands>
                        <command>sh /usr/springboot/app/start-jar.sh restart</command>
                    </commands>
                    <!-- 显示运行命令的输出结果 -->
                    <displayCommandOutputs>true</displayCommandOutputs>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

```xml
<!--
操作步骤1：
    先打包，执行mvn clean package
    再上传打包的文件，执行：wagon:upload-single
    执行shell 命令（脚本），主要是停止、删除原来的服务，启动新的服务，执行：wagon:shexec
    或者使用IDEA，新增maven程序，加入命令clean package wagon:upload-single wagon:sshexec
操作步骤2：
    只需要执行mvn clean package
    此处的 mvn clean package 相当于执行mvn clean package wagon:upload-single wagon:sshexec
-->
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>wagon-maven-plugin</artifactId>
    <version>2.0.2</version>
	<executions>
		<execution>				
			<id>upload-deploy</id>					
			<!-- 运行package打包的同时运行upload-single和sshexec -->
			<phase>package</phase>
			<goals>
				<goal>upload-single</goal>
				<goal>sshexec</goal>
			</goals>
				<configuration>
                   <!-- 拷贝目录下(执行目录) target目录下的jar包 -->
                   <fromFile>target/${project.artifactId}-${project.version}.jar</fromFile>
                   <!-- 使用scp传输文件 指定服务端 用户名密码 ip 并指定目标文件夹-->
                   <url>scp://${user}:${password}@${ip}/usr/springboot/app</url>
                   <!-- 命令列表 可以在传输完成后执行 -->
                   <commands>
                       <command>sh /usr/springboot/app/start-jar.sh restart</command>
                   </commands>
                   <!-- 显示运行命令的输出结果 -->
                   <displayCommandOutputs>true</displayCommandOutputs>
              </configuration>
		</execution>
	</executions>
</plugin>
```

```sh
#!/bin/bash

##报错重试次数
RETRYS=2
##超时退出，单位秒
TTL=300
##全部包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
ALL_ARRAY=('' '')
##常用包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
COMMON_ARRAY=('')
##快速包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
FAST_ARRAY_1=()
FAST_ARRAY_2=()
##输出日志默认名称
OUTPUT_LOGS_NAME=output.log
##服务默认路径
SERVER_DIR=$(cd `dirname $0`; pwd)'/'
##默认配置文件路径
#CONFIG_DIR=/opt/da/prod/
##默认路径:当前脚本路径
CUR_PATH=$(cd `dirname $0`; pwd)'/'

function stopProcess(){
	if [[ ! $1 ]];then
		echo '获取不到服务进程，请检查配置文件'
	else
		local PROCESS_NAME=$1
		local tpid=`ps -ef|grep $PROCESS_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			echo '正在关闭' $PROCESS_NAME '进程...'
			kill -9 $tpid
		fi
		sleep 1
		local tpid=`ps -ef|grep $PROCESS_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			echo '关闭' $PROCESS_NAME '进程失败，重试中...'
			kill -9 $tpid
		else
			echo '关闭 ' $PROCESS_NAME '进程成功!'
		fi
	fi
}

function startProcess(){
	local APP_NAME=$1 ## 服务名称
	local LOGS=$2	## 服务日志输出名称
	success=0
	fail=0
	sleep 1
	count=$[$count + 1]
	local num=$[RANDOM%10+1]
	local percent=$[$count + num]
	
	##实时进度百分比
	if [[ $percent -le 99 && ($percent -gt $tempslot || ! $tempslot) ]];then
		tempslot=$percent
		if [ $percent -lt 10 ];then
			printf "%d%%\b\b" $percent
		else
			printf "%d%%\b\b\b" $percent
		fi
	fi
	
	## 特殊服务特殊处理
	if [[ "$APP_NAME" == *"da-config"* ]];then
		tmsg=`cat ${LOGS} | grep 'Application version is -1: false' |awk '{print $2}'`
	else
		tmsg=`cat ${LOGS} | grep 'JVM running for' |awk '{print $2}'`
	fi
	
	if [[ ${tmsg} ]];then
		success=1
		if [[ $percent -ge 100 ]];then
			printf "%d%%\b\b\b" 100
		else
			for(( i=$percent; $i<=100; i++ ))
			do
				sleep 0.02
				printf "%d%%\b\b\b" $i
			done
		fi
	fi
	
	## 报错服务重试，当启动类型为快速启动则不执行重试
	if [[ $fail -eq 0 && $success -eq 0 && "${retry}" ]];then
		local tpid=`ps -ef|grep $APP_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ! ${tpid} ];then
			echo $APP_NAME '服务因未知错误而关闭，正在重试...'
			tempslot=0
			count=1
			if [[ $retry -eq 0 ]];then
				fail=1
				echo $APP_NAME '重试最大次数仍然失败，请手动启动！已跳过此服务，可检查进程是否存在'
			else
				start 1
			fi
		fi
	fi
	
	## 超时服务跳过
	if [[ $fail -eq 0 && $success -eq 0 && $count -eq $TTL ]];then
		fail=1
		echo $APP_NAME '启动超时！已跳过此服务，可检查进程是否存在'
	fi
	
	if [[ $fail -eq 0 && $success -eq 0 ]];then
		startProcess $APP_NAME $LOGS
	else
		tempslot=0
		count=1
		echo
	fi
}

function stop(){
	for var_app_name in ${ALL_ARRAY[*]}
	do
		stopProcess $var_app_name
	done
}

function start(){
	tempRetry=$1
	if [[ $tempRetry -eq 1 ]];then
		retry=$[$retry-1]
		echo
	else
		tempRetry=0
	fi
	check
	for var_app_name in ${ALL_ARRAY[*]}
	do
		if [[ $tempRetry -ne 1 ]];then
			retry=$RETRYS ##每个服务最大重试次数
		fi
		echo -n '正在启动' $var_app_name '...'
		run $var_app_name
		if [[ $success -eq 1 ]];then
			echo $var_app_name '启动完成!'
		fi
	done
	echo '服务已全部启动！'
	exit 0
}

## 检查服务是否启动
function check(){
	echo '正在初始化服务...'
	local tempArray=(${ALL_ARRAY[*]})
	for ((i=0;$i<${#tempArray[*]};i++))
	do
		local name=${tempArray[$i]}
		local tpid=`ps -ef|grep $name|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			unset ALL_ARRAY[$i]
		fi
	done
	echo '初始化服务完成'
}

function restart(){
	if [[ "${restartType}" == "all" ]];then
		echo "all"
	elif [[ "${restartType}" == "server" ]];then
		unset ALL_ARRAY
		ALL_ARRAY[0]=${COMMON_ARRAY[0]}
	elif [[ "${restartType}" == "web" ]];then
		unset ALL_ARRAY
		ALL_ARRAY[0]=${COMMON_ARRAY[1]}
	else
		unset ALL_ARRAY
		ALL_ARRAY=(${COMMON_ARRAY[*]})
	fi
	valid
	stop
	start
}

##快速启动
function faststart(){
	stop
	## 快速启动一 根据最后一个服务来获取关键日志
	echo -n "正在启动:${FAST_ARRAY_1[*]}..."
	local lastIndex=$((${#FAST_ARRAY_1[@]}-1))
	for var_app_name in ${FAST_ARRAY_1[*]}
	do
		if [[ "${FAST_ARRAY_1[lastIndex]}" == "$var_app_name" ]];then
			run $var_app_name
		else
			run $var_app_name 1
		fi
	done
	if [[ $success -eq 1 ]];then
		echo '${FAST_ARRAY_1[*]}启动完成!'
	fi
	echo '服务已全部启动！'
	exit 0
	## 有多少就重复写多少
}

function run(){
	local var_app_name=$1
	local isLog=$2
	local log=$OUTPUT_LOGS_NAME
	rm -f log $SERVER_DIR$log
	if [[ $isLog -eq 1 ]];then ##isLog等于1 代表不生成日志文件，并且不监听日志
		log=null
	fi
	
	if [[ "$var_app_name" == *"config"*  ]];then
			nohup java $JAVA_OPTS -jar -Dspring.config.location=$CONFIG_DIR,classpath:/application.yml $SERVER_DIR$var_app_name >$SERVER_DIR$log 2>&1 &
		else
			if [[ $isLog -ne 1 ]];then
				nohup java $JAVA_OPTS -jar $SERVER_DIR$var_app_name >$SERVER_DIR$log 2>&1 &
			else
				nohup java $JAVA_OPTS -jar $SERVER_DIR$var_app_name > null 2>&1 &
			fi
	fi
			
	## 特殊日志特殊处理
	##if [[ "$var_app_name" == *"da-server"* ]];then
	##		log=server.txt
	##		nohup java $JAVA_OPTS -Xdebug -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8887 -jar $SERVER_DIR$var_app_name >$SERVER_DIR$log 2>&1 &
	##	elif [[ "$var_app_name" == *"da-web"*  ]];then
	##		log=web.txt
	##		nohup java $JAVA_OPTS -jar $SERVER_DIR$var_app_name >$SERVER_DIR$log 2>&1 &
	##	elif [[ "$var_app_name" == *"da-config"*  ]];then
	##		nohup java $JAVA_OPTS -jar -Dspring.config.location=$CONFIG_DIR,classpath:/application.yml $SERVER_DIR$var_app_name >$SERVER_DIR$log 2>&1 &
	##	else
	##		if [[ $isLog -ne 1 ]];then
	##			nohup java $JAVA_OPTS -jar $SERVER_DIR$var_app_name >$SERVER_DIR$log 2>&1 &
	##		else
	##			nohup java $JAVA_OPTS -jar $SERVER_DIR$var_app_name > null 2>&1 &
	##		fi
	##		
	##fi
		
	if [[ $isLog -ne 1 ]];then
		startProcess $var_app_name $SERVER_DIR$log	
	fi

	rm -f log $SERVER_DIR$OUTPUT_LOGS_NAME	
}

##java 环境信息
function java(){
	local JAVA_PATH=`which java`
	local JAVA_VERSION=`$JAVA_PATH -version`
	echo "JAVA安装路径：$JAVA_PATH"
	echo "${JAVA_VERSION}"
}

##java 进程信息
function status(){
	local tempArray=(${ALL_ARRAY[*]})
	## 用户 进程号 进程的cpu占用率 进程的内存占用率 进程所使用的虚存大小 进程实际内存大小Kb  进程的状态 进程启动时间 进程使用总cpu时间 服务路径
	printf '%-8s %-10s %-12s %-10s %-10s %-10s %-5s %-8s %-10s %-10s\n' "用户" "进程号" "cpu占用率" "内存占用率" "虚存大小Kb" "实际内存大小Kb" "进程启动时间" "使用总cpu时间" "服务路径" 
	for ((i=0;$i<${#tempArray[*]};i++))
	do
		local name=${tempArray[$i]}
		## local P_INFO=`ps -aux|grep $name|grep -v grep|grep -v kill|awk '{printf "%-8s %-8s %-10s %-10s %-10s %-10s %-5s %-8s %-10s %-30s",$1,$2,$3,$4,$5,$6}'`
		local P_INFO=`ps -aux|grep $name|grep -v grep|grep -v kill`
		if [[ $P_INFO ]];then
			echo -n "$P_INFO" | awk '{printf "%-6s %-8s%-10s %-8s %-10s %-16s %-8s %-12s",$1,$2,$3,$4,$5,$6,$9,$10}'
			prinf "%-s" $SERVER_DIR$name
		else
			echo "$SERVER_DIR$name 服务已关闭"
		fi
	done
}

## 检验当前目录是否存在jar包，不存在则报错
function valid(){
config
echo '正在检测'$SERVER_DIR'路径下的服务是否齐全...'
	local tempArray=(${ALL_ARRAY[*]})
	for ((i=0;$i<${#tempArray[*]};i++))
	do
		local name=${tempArray[$i]}
		if [ -f  $SERVER_DIR/$name ];then
			echo -e '\033[42m' $name'检测成功!' '\033[0m'
		else
			NOTFOUNF=1
			echo -e '\033[31m' $SERVER_DIR'路径下不存在服务：'$name '\033[0m'		
		fi
	done
if [[ $NOTFOUNF -eq 1]];then
	exit 1
fi
}

function config(){
	if [[ "$CONFIG_PATH" == "config" || "${restartType}" == "config" ]];then
		read -p '请输入服务路径(默认是脚本路径'$CUR_PATH')：' SERVER_DIR
		read -p '请输入服务路径(默认是脚本路径'$CUR_PATH'/prod)：' CONFIG_DIR
		read -p '请输入报错重试次数(默认是'$RETRYS')：' TEM_C
		read -p '请输入超时时间(默认是'$TTL')：' TEM_S
		read -p '请输入jvm配置(默认无)：' JAVA_OPTS
	fi
}

# 使用 -z 可以判断一个变量是否为空；
		if [[ ! $SERVER_DIR ]];then
			SERVER_DIR=$CUR_PATH
		fi
		if [[ "${SERVER_DIR: -1}" != "/" ]];then
			SERVER_DIR=$SERVER_DIR'/'
		fi
		if [[ ! $CONFIG_DIR ]];then
			CONFIG_DIR=$CUR_PATH/prod/
		fi
		if [[ "${CONFIG_DIR: -1}" != "/" ]];then
			CONFIG_DIR=$CONFIG_DIR'/'
		fi
		if [[ $TEM_C ]];then
			RETRYS=$TEM_C
		fi
		if [[ $TEM_S ]];then
			TTL=$TEM_S
		fi

CONFIG_PATH=$2
case "$1" in
	'start')
	valid
	start
	;;
	'stop')
	stop
	;;
	'restart')
	restartType=$2
	CONFIG_PATH=$3
	restart
	;;
	'faststart')
	valid
	faststart
	;;
	'status')
	status
	;;
	'java')
	echo "JAVA版本信息："
	java
	;;
	*)
	echo "Usage: $0 {|start|stop|restart|faststart|java}"
	echo "==================命令指南================"
	echo "start:正常启动所有服务，已经启动的服务不会再次启动，若中途出现报错则会自动重试，重试次数默认2次，服务启动超时则跳过继续下一个服务"
	echo "stop:正常关闭所有服务"
	echo "restart: all-启动所有服务 | server-单独启动server服务 | web-单独启动web服务 | 默认启动server和web两个服务"
	echo "faststart: 快速启动所有服务，报错不会自动重试，服务启动超时则跳过继续下一个服务"
	echo "status:查看服务状态及基本信息"
	echo "java:查看java基本信息"
	echo "java:配置文件形式"
	exit 1
	;;
esac
```

## 4.自动化脚本shell，外部文件

```shell
#!/bin/bash

##报错重试次数
RETRYS=2
##超时退出，单位秒
TTL=300
##全部包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
ALL_ARRAY=()
##常用包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
COMMON_ARRAY=()
##快速包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
FAST_ARRAY_1=()
FAST_ARRAY_2=()
##输出日志默认名称
OUTPUT_LOGS_NAME=output.txt
##服务默认路径
SERVER_DIR=$(cd `dirname $0`; pwd)'/'
##默认配置文件路径
#CONFIG_DIR=/opt/da/prod/
##默认路径:当前脚本路径
CUR_PATH=$(cd `dirname $0`; pwd)'/'
##默认监听日志关键内容
DEFAULT_LISTEN='JVM running for'

function stopProcess(){
	if [[ ! $1 ]];then
		echo '获取不到服务进程，请检查配置文件'
	else
		local PROCESS_NAME=$1
		local tpid=`ps -ef|grep $PROCESS_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			echo '正在关闭' $PROCESS_NAME '进程...'
			kill -9 $tpid
		fi
		sleep 1
		local tpid=`ps -ef|grep $PROCESS_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			echo '关闭' $PROCESS_NAME '进程失败，重试中...'
			kill -9 $tpid
		else
			echo '关闭 ' $PROCESS_NAME '进程成功!'
		fi
	fi
}

function startProcess(){
	local APP_NAME=$1 ## 服务名称
	local LOGS=$2	## 服务日志输出名称
	local LISTEN_SERVER=$3	## 日志监听启动成功关键内容
	local LISTEN_URL=$4	## TOMCAT服务监听
	success=0
	fail=0
	sleep 1
	count=$[$count + 1]
	local num=$[RANDOM%10+1]
	local percent=$[$count + num]
	
	##实时进度百分比
	if [[ $percent -le 99 && ($percent -gt $tempslot || ! $tempslot) ]];then
		tempslot=$percent
		if [ $percent -lt 10 ];then
			printf "%d%%\b\b" $percent
		else
			printf "%d%%\b\b\b" $percent
		fi
	fi
	
	## 特殊服务特殊处理
	if [[ $LISTEN_URL ]];then
		if [[ $(curl -sIL -w "%{http_code}" -o /dev/null $LISTEN_URL) -eq 200 ]];then
			tmsg="200"
		fi
	elif [[ $LOGS ]];then
		echo "server"
		tmsg=`cat ${LOGS} | grep $LISTEN_SERVER |awk '{print $2}'`
	else
		echo '获取不到启动监听配置，当前服务仍在后台启动，脚本即将退出...'
		sleep 2
		exit 1
	fi
	
	if [[ ${tmsg} ]];then
		success=1
		if [[ $percent -ge 100 ]];then
			printf "%d%%\b\b\b" 100
		else
			for(( i=$percent; $i<=100; i++ ))
			do
				sleep 0.02
				printf "%d%%\b\b\b" $i
			done
		fi
	fi
	
	## 报错服务重试，当启动类型为快速启动则不执行重试
	if [[ $fail -eq 0 && $success -eq 0 && "${retry}" ]];then
		local tpid=`ps -ef|grep $APP_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ! ${tpid} ];then
			echo $APP_NAME '服务因未知错误而关闭，正在重试...'
			tempslot=0
			count=1
			if [[ $retry -eq 0 ]];then
				fail=1
				echo $APP_NAME '重试最大次数仍然失败，请手动启动！已跳过此服务，可检查进程是否存在'
			else
				start 1
			fi
		fi
	fi
	
	## 超时服务跳过
	if [[ $fail -eq 0 && $success -eq 0 && $count -eq $TTL ]];then
		fail=1
		echo $APP_NAME '启动超时！已跳过此服务，可检查进程是否存在'
	fi

	if [[ $fail -eq 0 && $success -eq 0 ]];then
		startProcess $APP_NAME $LOGS $LISTEN_SERVER $LISTEN_URL
	else
		tempslot=0
		count=1
		echo
	fi
}

function stop(){
	for var_app_name in ${ALL_ARRAY[*]}
	do
		stopProcess $var_app_name
	done
	for var_tomccat_name in ${TOMCAT_ARRAY[*]}
	do
		local TEMP_TOMCAT_SERVER=TOMCAT_SERVER_$var_tomccat_name		##tomcat服务名称
		local TOMCAT_SERVER=`eval echo '$'"${TEMP_TOMCAT_SERVER}"`
		stopProcess $TOMCAT_SERVER
	done
}

function start(){
	tempRetry=$1
	if [[ $tempRetry -eq 1 ]];then
		retry=$[$retry-1]
		echo
	else
		tempRetry=0
	fi
	check
	## springboot服务启动
	for var_app_name in ${ALL_ARRAY[*]}
	do
		if [[ $tempRetry -ne 1 ]];then
			retry=$RETRYS ##每个服务最大重试次数
		fi
		echo -n '正在启动' $var_app_name '...'
		run $var_app_name
		if [[ $success -eq 1 ]];then
			echo $var_app_name '启动完成!'
		fi
	done
	startTomcat
	echo '服务已全部启动！'
	exit 0
}

## 检查服务是否启动
function check(){
	echo '正在初始化服务...'
	local tempArray=(${ALL_ARRAY[*]})
	for ((i=0;$i<${#tempArray[*]};i++))
	do
		local name=${tempArray[$i]}
		local tpid=`ps -ef|grep $name|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			unset ALL_ARRAY[$i]
		fi
	done

	for ((i=0;$i<${#TOMCAT_ARRAY[*]};i++))
	do
		local name=${TOMCAT_ARRAY[$i]}
		local TEMP_TOMCAT_SERVER=TOMCAT_SERVER_$name		##tomcat服务名称
		local TOMCAT_SERVER=`eval echo '$'"${TEMP_TOMCAT_SERVER}"`
		validParam $TOMCAT_SERVER $TEMP_TOMCAT_SERVER
		local tpid=`ps -ef|grep $TOMCAT_SERVER|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			unset TOMCAT_ARRAY[$i]
		fi
	done
	echo '初始化服务完成'
}

## 检验参数是否配置
function validParam(){
	local name=$1
	local param=$2
	if [[ ! $name ]];then
		echo "$param 参数未配置"
		exit 1
	fi
}

function restart(){

	if [[ "${restartType}" == "all" ]];then
		echo "all"
	elif [[ "${restartType}" == "server" ]];then
		unset ALL_ARRAY
		ALL_ARRAY[0]=${COMMON_ARRAY[0]}
	elif [[ "${restartType}" == "web" ]];then
		unset ALL_ARRAY
		ALL_ARRAY[0]=${COMMON_ARRAY[1]}
	else
		unset ALL_ARRAY
		ALL_ARRAY=(${COMMON_ARRAY[*]})
	fi
	valid
	stop
	start
}


##快速启动
function faststart(){
	stop
	## 快速启动一 根据最后一个服务来获取关键日志
	echo -n "正在启动:${FAST_ARRAY_1[*]}..."
	local lastIndex=$((${#FAST_ARRAY_1[@]}-1))
	for var_app_name in ${FAST_ARRAY_1[*]}
	do
		if [[ "${FAST_ARRAY_1[lastIndex]}" == "$var_app_name" ]];then
			run $var_app_name
		else
			run $var_app_name 1
		fi
	done
	if [[ $success -eq 1 ]];then
		echo '${FAST_ARRAY_1[*]}启动完成!'
	fi
	echo '服务已全部启动！'
	exit 0
	## 有多少就重复写多少
}

##启动tomcat
function startTomcat(){
	## tomcat服务启动
	for var_app_name in ${TOMCAT_ARRAY[*]}
	do
		if [[ $tempRetry -ne 1 ]];then
			retry=$RETRYS ##每个服务最大重试次数
		fi
		echo -n '正在启动' $var_app_name '...'
		runTomcat $var_app_name
		if [[ $success -eq 1 ]];then
			echo $var_app_name '启动完成!'
		fi
	done
}

function runTomcat(){
	local var_app_name=$1
	local param=${var_app_name%%.*} ## 从后面去除后缀最后一个.
	local param=${param%-*}			## 从后面去除后缀第一个-
	local param=${param/-/_}		## 把剩余的—转换为_
	local TEMP_TOMCAT_START=TOMCAT_START_$param	## tomcat启动路径
	local TOMCAT_START=`eval echo '$'"${TEMP_TOMCAT_START}"`
	local TEMP_TOMCAT_URL=TOMCAT_URL_$param		##tomcat监听地址
	local TOMCAT_URL=`eval echo '$'"${TEMP_TOMCAT_URL}"`
	local TEMP_TOMCAT_SERVER=TOMCAT_SERVER_$param		##tomcat服务名称
	local TOMCAT_SERVER=`eval echo '$'"${TEMP_TOMCAT_SERVER}"`
	validParam $TOMCAT_SERVER $TEMP_TOMCAT_SERVER
	validParam $TOMCAT_START $TEMP_TOMCAT_START
	validParam $TOMCAT_URL $TEMP_TOMCAT_URL
	sh $TOMCAT_START
	startProcess $TOMCAT_SERVER -1 -1 $TOMCAT_URL
}

function run(){
	local var_app_name=$1
	local isLog=$2
	local param=${var_app_name%%.*} ## 从后面去除后缀最后一个.
	local param=${param%-*}			## 从后面去除后缀第一个-
	local param=${param/-/_}		## 把剩余的—转换为_
	local TEMP_LOGS=LOGS_$param		## 日志名称
	local log=`eval echo '$'"${TEMP_LOGS}"`
	local TEMP_JAVA_OPTS=JAVA_OPTS_$param		## jvm参数
	local JAVA_OPTS=`eval echo '$'"${TEMP_JAVA_OPTS}"`
	local TEMP_LISTEN=LOGS_$param		## 监听日志
	local LISTEN=`eval echo '$'"${TEMP_LISTEN}"`
	local TEMP_SPRING_CONFIG=SPRING_CONFIG_$param	## spring配置参数
	local SPRING_CONFIG=`eval echo '$'"${TEMP_SPRING_CONFIG}"`

	if [[ ! $log ]];then 
		log=$OUTPUT_LOGS_NAME
	fi
	if [[ ! $JAVA_OPTS ]];then 
		JAVA_OPTS=$DEFAULT_JAVA_OPTS
	fi
	if [[ ! $LISTEN ]];then 
		LISTEN=$DEFAULT_LISTEN
	fi

	if [[ $isLog -ne 1 ]];then
		rm -f log $SERVER_DIR$log
		nohup java $JAVA_OPTS -jar $SPRING_CONFIG $SERVER_DIR$var_app_name >$SERVER_DIR$log 2>&1 &
		startProcess $var_app_name $SERVER_DIR$log $LISTEN
		rm -f log $SERVER_DIR$OUTPUT_LOGS_NAME
	else
		nohup java $JAVA_OPTS -jar $SPRING_CONFIG $SERVER_DIR$var_app_name > /dev/null 2>&1 &
	fi	
}

##java 环境信息
function java(){
	local JAVA_PATH=`which java`
	local JAVA_VERSION=`$JAVA_PATH -version`
	echo "JAVA安装路径：$JAVA_PATH"
	echo "${JAVA_VERSION}"
}

##java 进程信息
function status(){
	local tempArray=(${ALL_ARRAY[*]})
	## 用户 进程号 进程的cpu占用率 进程的内存占用率 进程所使用的虚存大小 进程实际内存大小Kb  进程的状态 进程启动时间 进程使用总cpu时间 服务路径
	printf '%-8s %-10s %-12s %-10s %-10s %-10s %-5s %-8s %-10s %-10s\n' "用户" "进程号" "cpu占用率" "内存占用率" "虚存大小Kb" "实际内存大小Kb" "进程启动时间" "使用总cpu时间" "服务路径" 
	for ((i=0;$i<${#tempArray[*]};i++))
	do
		local name=${tempArray[$i]}
		## local P_INFO=`ps -aux|grep $name|grep -v grep|grep -v kill|awk '{printf "%-8s %-8s %-10s %-10s %-10s %-10s %-5s %-8s %-10s %-30s",$1,$2,$3,$4,$5,$6}'`
		local P_INFO=`ps -aux|grep $name|grep -v grep|grep -v kill`
		if [[ $P_INFO ]];then
			echo -n "$P_INFO" | awk '{printf "%-6s %-8s%-10s %-8s %-10s %-16s %-8s %-12s",$1,$2,$3,$4,$5,$6,$9,$10}'
			echo $SERVER_DIR$name
		else
			echo "$SERVER_DIR$name 服务已关闭"
		fi
	done
}

## 检验当前目录是否存在jar包，不存在则报错
function valid(){
config
echo '正在检测'$SERVER_DIR'路径下的服务是否齐全...'
	local tempArray=(${ALL_ARRAY[*]})
	for ((i=0;$i<${#tempArray[*]};i++))
	do
		local name=${tempArray[$i]}
		if [ -f  $SERVER_DIR/$name ];then
			echo -e '\033[42m' $name'检测成功!' '\033[0m'
		else
			NOTFOUNF=1
			echo -e '\033[31m' $SERVER_DIR'路径下不存在服务：'$name '\033[0m'		
		fi
	done
if [[ $NOTFOUNF -eq 1 ]];then
	exit 1
fi
}

function config(){
	if [[ "$CONFIG_PATH" == "config" || "${restartType}" == "config" ]];then
		read -p '请输入服务路径(默认是脚本路径'$CUR_PATH')：' SERVER_DIR
		read -p '请输入服务路径(默认是脚本路径'$CUR_PATH'/prod)：' CONFIG_DIR
		read -p '请输入报错重试次数(默认是'$RETRYS')：' TEM_C
		read -p '请输入超时时间(默认是'$TTL')：' TEM_S
		read -p '请输入jvm配置(默认无)：' DEFAULT_JAVA_OPTS
	fi
}

while read line;do
	eval "$line"
done <	springCloud.config

# 使用 -z 可以判断一个变量是否为空；
		if [[ ! $SERVER_DIR ]];then
			SERVER_DIR=$CUR_PATH
		fi
		if [[ "${SERVER_DIR: -1}" != "/" ]];then
			SERVER_DIR=$SERVER_DIR'/'
		fi
		if [[ ! $CONFIG_DIR ]];then
			CONFIG_DIR=$CUR_PATH/prod/
		fi
		if [[ "${CONFIG_DIR: -1}" != "/" ]];then
			CONFIG_DIR=$CONFIG_DIR'/'
		fi
		if [[ $TEM_C ]];then
			RETRYS=$TEM_C
		fi
		if [[ $TEM_S ]];then
			TTL=$TEM_S
		fi				

CONFIG_PATH=$2
case "$1" in
	'start')
	valid
	start
	;;
	'stop')
	stop
	;;
	'restart')
	restartType=$2
	CONFIG_PATH=$3
	restart
	;;
	'faststart')
	valid
	faststart
	;;
	'status')
	status
	;;
	'java')
	echo "JAVA版本信息："
	java
	;;
	'tomcat')
	startTomcat 'start'
	;;
	*)
	echo "Usage: $0 {|start|stop|restart|faststart|java}"
	echo "==================命令指南================"
	echo "start:正常启动所有服务，已经启动的服务不会再次启动，若中途出现报错则会自动重试，重试次数默认2次，服务启动超时则跳过继续下一个服务"
	echo "stop:正常关闭所有服务"
	echo "restart: all-启动所有服务 | server-单独启动server服务 | web-单独启动web服务 | 默认启动server和web两个服务"
	echo "faststart: 快速启动所有服务，报错不会自动重试，服务启动超时则跳过继续下一个服务"
	echo "status:查看服务状态及基本信息"
	echo "java:查看java基本信息"
	echo "java:配置文件形式"
	exit 1
	;;
esac
```

```txt
##全部包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
ALL_ARRAY=('da-server-1.0.0.jar' '')
##常用包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
COMMON_ARRAY=('')
##快速包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
FAST_ARRAY_1=()
FAST_ARRAY_2=()
##输出日志默认名称，默认output.txt
OUTPUT_LOGS_NAME=output.txt
##存放服务的默认路径，默认是脚本所在路径
##SERVER_DIR=/opt/da/
##报错重试次数，选填，默认2次
##RETRYS=2
##超时退出，单位秒，选填，默认300秒
##TTL=300
##默认监听日志启动关键内容
##DEFAULT_LISTEN='JVM running for'

################以下为服务的配置##################
##服务命名规则：如da-server-1.0.0.jar，则取名为da_server,注意，必须为下划线
##LOGS_服务名称前缀：日志名称
##LISTEN_服务名称前缀：日志监听文本，启动成功的关键内容
##JAVA_OPTS_服务名称前缀：服务jvm参数
##SPRING_CONFIG_服务名称前缀：spring参数

LISTEN_da_config="Application version is -1: false"
SPRING_CONFIG_da_config="-Dspring.config.location=/opt/da/prod/,classpath:/application.yml"

LOGS_da_server=server.txt
JAVA_OPTS_da_server="-Xdebug -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8887"

LOGS_ROOT=web.txt

############Tomcat服务配置##############
##服务命名规则：如da-server-1.0.0.jar，则取名为da_server,注意，必须为下划线
TOMCAT_ARRAY=()
TOMCAT_SERVER_ROOT='apache-tomcat-9.0.39'
TOMCAT_START_ROOT='F:/Download/apache-tomcat-9.0.39/bin'
TOMCAT_URL_ROOT=‘http://127.0.0.1:38080’

############end此行勿删##############
```

