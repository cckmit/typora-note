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
	echo "start:正常启动所有服务，已经启动的服务不会再次启动，若中途出现报错则会自动重试,服务启动超时则跳过继续下一个服务"
	echo "stop:正常关闭所有服务"
	echo "restart: all-启动所有服务 | 输入COMMON_ARRAY下标单独重启服务 | 默认启动日常服务(COMMON_ARRAY)的配置"
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

##配置文件名称
SCRIPT_CONFIG='springCloud.config'
##默认版本号
SERVER_VERSION='-1.0.0'
##报错重试次数
RETRYS=2
##超时退出，单位秒
TTL=300
##全部包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
ALL_ARRAY=()
TOMCAT_ARRAY=()
##常用包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
COMMON_ARRAY=()
##快速包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
FAST_ARRAY=()
##日志默认路径
LOGS_DIR=$(cd `dirname $0`; pwd)'/'
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
##计时
TOTAL_TIME_SYS=0

# 定义颜色变量
RED='\e[1;31m' # 红
GREEN='\e[1;32m' # 绿
DEEP_GREEN='\e[96m' #黑底青色
BACK_RED='\e[41m' #红底白字
YELLOW='\e[1;33m' # 黄
BLUE='\e[1;34m' # 蓝
PINK='\e[1;35m' # 粉红
RES='\e[0m' # 清除颜色

function stopProcess(){
	if [[ ! $1 ]];then
		echo -e "${RED}获取不到服务进程，请检查配置文件${RES}"
		exit 1
	else
		local PROCESS_NAME=$1
		local tpid=`ps -ef|grep "$PROCESS_NAME"|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			echo -e "${YELLOW}正在关闭 $PROCESS_NAME 进程...${RES}"
			kill -9 $tpid
		fi
		sleep 1
		local tpid=`ps -ef|grep "$PROCESS_NAME"|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			echo -e "${YELLOW}关闭 $PROCESS_NAME 进程失败，重试中...${RES}"
			kill -9 $tpid
		else
			echo -e "${GREEN}关闭 $PROCESS_NAME 进程成功!${RES}"
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
	if [[ $percent -le 99 && ($percent -gt $tempslot || $tempslot -eq 0) ]];then
		tempslot=$percent
		if [ $percent -lt 10 ];then
			printf "%d%%\b\b" $percent
		else
			printf "%d%%\b\b\b" $percent
		fi
	fi
	
	## 特殊服务特殊处理
	if [[ $LISTEN_URL ]];then
		##-I/--head 仅返回消息头部，使用HEAD请求
		##-s/--slient 减少输出的信息，比如进度
		##--connect-timeout <seconds> 指定尝试连接的最大时长
		##-m/--max-time <seconds> 指定处理的最大时长
		##-w "参数"  自定义curl的输出
		##-o/--output <file> 指定输出文件名称
		if [[ $(curl -I -s --connect-timeout 1 -m 1 -w "%{http_code}" -o /dev/null $LISTEN_URL) -eq 200 ]];then
			tmsg=200
		fi
	elif [[ $LOGS ]];then
		tmsg=`cat ${LOGS} | grep "$LISTEN_SERVER" |awk '{print $2}'`
		if [[ ${tmsg} ]];then
			tmsg=200
		fi
	else
		echo -e "${YELLOW}获取不到启动监听配置，当前服务仍在后台启动，脚本即将退出...${RES}"	
		sleep 2
		exit 1
	fi
	
	if [[ ${tmsg} -eq 200 ]];then
		success=1
		if [[ $percent -ge 100 ]];then
			printf "%d%%\b\b\b" 100
		else
			for(( i=$percent; $i<=100; i++ ))
			do
				sleep 0.01
				if [ $i -lt 10 ];then
					printf "%d%%\b\b" $i
				else
					printf "%d%%\b\b\b" $i
				fi
			done
		fi
	fi
	
	## 报错服务重试，当启动类型为快速启动则不执行重试
	if [[ $fail -eq 0 && $success -eq 0 && "${retry}" ]];then
		local tpid=`ps -ef|grep $APP_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ! ${tpid} ];then
			echo -e "${YELLOW}$APP_NAME服务因未知错误而关闭，正在重试...${RES}"		
			tempslot=0
			count=1
			if [[ $retry -eq 0 ]];then
				fail=1
				echo -e "${RED}$APP_NAME 重试最大次数仍然失败，请手动启动！已跳过此服务，可检查进程是否存在${RES}"	
			else
				start 1
			fi
		fi
	fi
	
	## 超时服务跳过
	if [[ $fail -eq 0 && $success -eq 0 && $count -eq $TTL ]];then
		fail=1
		echo -e "${RED}$APP_NAME 启动超时！已跳过此服务，可检查进程是否存在${RES}"
	fi

	if [[ $fail -eq 0 && $success -eq 0 ]];then
		startProcess "$APP_NAME" "$LOGS" "$LISTEN_SERVER" "$LISTEN_URL"
	else
		tmsg=500
		tempslot=0
		count=1
		echo
	fi
}

function stop(){
	for var_app_name in ${ALL_ARRAY[*]}
	do
		stopProcess "$var_app_name"
	done
	for var_tomccat_name in ${TOMCAT_ARRAY[*]}
	do
		local TEMP_TOMCAT_SERVER=TOMCAT_SERVER_$var_tomccat_name		##tomcat服务名称
		local TOMCAT_SERVER=`eval echo '$'"${TEMP_TOMCAT_SERVER}"`
		stopProcess "$TOMCAT_SERVER"
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
		echo -n -e "${YELLOW}正在启动' $var_app_name ...${RES}"
		START_TIME_SYS=`date +%s`
		run $var_app_name
		END_TIME_SYS=`date +%s`
		SUM_TIME_SYS=$[ $END_TIME_SYS - $START_TIME_SYS ]
		TOTAL_TIME_SYS=$[$TOTAL_TIME_SYS + $SUM_TIME_SYS ]
		if [[ $success -eq 1 ]];then
			echo -e "${GREEN}$var_app_name 启动完成!耗时:${SUM_TIME_SYS}秒。${RES}"
		fi
	done
	startTomcat
	echo -e "${GREEN}服务已全部启动！总共耗时:${TOTAL_TIME_SYS}秒。${RES}"
	exit 0
}

## 检查服务是否启动
function check(){
	echo -e "${YELLOW}正在初始化服务...${RES}"
	local tempArray=(${ALL_ARRAY[*]})
	for ((i=0;$i<${#tempArray[*]};i++))
	do
		local name=${tempArray[$i]}
		local tpid=`ps -ef|grep $name|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			unset ALL_ARRAY[$i]
		fi
	done

	local tempArray=(${TOMCAT_ARRAY[*]})
	for ((i=0;$i<${#tempArray[*]};i++))
	do
		local name=${tempArray[$i]}
		local TEMP_TOMCAT_SERVER=TOMCAT_SERVER_$name		##tomcat服务名称
		local TOMCAT_SERVER=`eval echo '$'"${TEMP_TOMCAT_SERVER}"`
		## validParam "$TOMCAT_SERVER" "$TEMP_TOMCAT_SERVER"
		local tpid=`ps -ef|grep $TOMCAT_SERVER|grep -v grep|grep -v kill|awk '{print $2}'`
		if [ ${tpid} ];then
			unset TOMCAT_ARRAY[$i]
		fi
	done
	echo -e "${YELLOW}初始化服务完成${RES}"
}

## 检验参数是否配置
function validParam(){
	local name=$1
	local param=$2
	if [[ ! $name ]];then
		NOTFOUNF=1
		echo -e "${RED}$param 参数未配置${RES}"
	fi
}

function restart(){
	echo -e "${GREEN}所有服务:${ALL_ARRAY[*]} ${TOMCAT_ARRAY[*]}${RES}"
	echo -e "${GREEN}常用服务:${COMMON_ARRAY[*]} ${RES}"
	echo -e "${YELLOW}输入常用服务下标索引指定服务重启：如：输入0，代表启动第一个服务${RES}"
	echo -e "${YELLOW}-1：退出；all：重启所有服务；不输入默认重启日常服务${RES}"
	read -ep "请输入常用服务索引(多个服务用逗号隔开):" restartType
	restartType=(${restartType//,/ })
	if [[ "${restartType}" == "all" ]];then
		echo "all"
	elif [[ "${restartType}" =~ ^[-]?[0-9][0-9]*$ && $restartType ]];then
		local lastIndex=$((${#COMMON_ARRAY[@]}-1))
		if [[ "$restartType" -le -1 ]];then
			echo -e "${GREEN}正常退出${RES}"
			exit 0
		elif [[ "$restartType" -lt ${#COMMON_ARRAY[@]} ]];then
			for ((q=0;$q<${#restartType[*]};q++))
			do
				local inde=${restartType[$q]}
				if [[ "$inde" -ge ${#COMMON_ARRAY[@]} ]];then
					echo -e "${RED}常用服务下标输入过大，最大为下标索引 ${lastIndex}${RES}"
					exit 1
				elif [[ "$inde" -lt 0 ]];then
					echo -e "${GREEN}正常退出${RES}"
					exit 0
				else
					filterServer "${COMMON_ARRAY[$inde]}"
				fi
			done
			ALL_ARRAY=(${TEMP_ALL_ARRAY[*]})
			TOMCAT_ARRAY=(${TEMP_TOMCAT_ARRAY[*]})
		else
			echo -e "${RED}常用服务下标输入过大，最大为下标索引 ${lastIndex}${RES}"
			exit 1
		fi
	else
		for ((j=0;$j<${#COMMON_ARRAY[*]};j++))
		do
			filterServer "${COMMON_ARRAY[$j]}"
		done
		 ALL_ARRAY=(${TEMP_ALL_ARRAY[*]})
		 TOMCAT_ARRAY=(${TEMP_TOMCAT_ARRAY[*]})
	fi
	valid
	stop
	start
}

##过滤服务
function filterServer(){
	local server_name=$1
	##unset ALL_ARRAY
	for ((i=0;$i<${#ALL_ARRAY[*]};i++))
	do
		local name=${ALL_ARRAY[$i]}
		if [[ "${name}" == "${server_name}" ]];then
			TEMP_ALL_ARRAY[${#TEMP_ALL_ARRAY[@]}]="${name}"
			break
		fi
	done

	##unset TOMCAT_ARRAY
	for ((i=0;$i<${#TOMCAT_ARRAY[*]};i++))
	do
		local name=${TOMCAT_ARRAY[$i]}
		if [[ "${name}" == "${server_name}" ]];then
			TEMP_TOMCAT_ARRAY[${#TEMP_TOMCAT_ARRAY[@]}]="${name}"
			break
		fi
	done
}

##快速启动
function faststart(){
	stop
	## 快速启动一 根据最后一个服务来获取关键日志
	echo -e "${YELLOW}正在启动:${FAST_ARRAY[*]}...${RES}"
	for var_arr in ${FAST_ARRAY[*]}
	do
		var_arr=(${var_arr//,/ })
		echo ${var_arr[*]}
		local lastIndex=$((${#var_arr[@]}-1))
		for ((i=0;$i<${#var_arr[*]};i++))
		do	
			local var_app_name=${var_arr[$i]}
			if [[ "${var_arr[lastIndex]}" == "$var_app_name" ]];then
				local server_type=$(filterServer "${var_arr[$i]}")
				runFast "$server_type" "$var_app_name"
			else
				local server_type=$(filterServer "${var_arr[$i]}")
				runFast "$server_type" "$var_app_name" 1
			fi
		done
	done
	
	if [[ $success -eq 1 ]];then
		echo -e "${GREEN}${FAST_ARRAY_1[*]}启动完成!${RES}"
	fi
	echo -e "${GREEN}服务已全部启动！${RES}"
	exit 0
}

function runFast(){
	local server_type=$1
	local server_name=$2
	if [[ "${server_type}" == "server" ]];then
		run $server_name $3
	elif [[ "${server_type}" == "tomcat" ]];then
		runTomcat $server_name 
	else
		echo -e "${RED}$server_name 服务不存在！${RES}"
		exit 1
	fi
}

##启动tomcat
function startTomcat(){
	## tomcat服务启动
	for var_app_name in ${TOMCAT_ARRAY[*]}
	do
		if [[ $tempRetry -ne 1 ]];then
			retry=$RETRYS ##每个服务最大重试次数
		fi
		echo -n -e "${YELLOW}正在启动$var_app_name ...${RES}"
		START_TIME_SYS=`date +%s`
		runTomcat $var_app_name
		END_TIME_SYS=`date +%s`
		SUM_TIME_SYS=$[ $END_TIME_SYS - $START_TIME_SYS ]
		TOTAL_TIME_SYS=$[$TOTAL_TIME_SYS + $SUM_TIME_SYS ]
		if [[ $success -eq 1 ]];then
			echo -e "${GREEN}$var_app_name启动完成!耗时:${SUM_TIME_SYS}秒。${RES}"
		fi
	done
}

function runTomcat(){
	local var_app_name=$(formatParam "$1")
	local TEMP_TOMCAT_START=TOMCAT_START_$var_app_name	## tomcat启动路径
	local TOMCAT_START=`eval echo '$'"${TEMP_TOMCAT_START}"`
	local TEMP_TOMCAT_URL=TOMCAT_URL_$var_app_name		##tomcat监听地址
	local TOMCAT_URL=`eval echo '$'"${TEMP_TOMCAT_URL}"`
	local TEMP_TOMCAT_SERVER=TOMCAT_SERVER_$var_app_name		##tomcat服务名称
	local TOMCAT_SERVER=`eval echo '$'"${TEMP_TOMCAT_SERVER}"`
	local TEMP_REMOTE=LISTEN_REMOTE_URL_$var_app_name	## 远程地址监听
	local REMOTE_URL=`eval echo '$'"${TEMP_REMOTE}"`
	
	if [[ $REMOTE_URL ]];then 
		echo
		echo -n -e "${DEEP_GREEN}正在监听远程地址$REMOTE_URL...${RES}"
		listenRemote "$REMOTE_URL"
		echo -n -e "${DEEP_GREEN}远程地址$REMOTE_URL启动成功，正在启动$var_app_name...${DEEP_GREEN}"
	fi
	
	sh $TOMCAT_START  > /dev/null
	startProcess $TOMCAT_SERVER -1 -1 $TOMCAT_URL
}

function run(){
	local var_app_name=$1
	local name=$(formatParam "$var_app_name")
	local isLog=$2
	local TEMP_LOGS=LOGS_$name		## 日志名称
	local log=`eval echo '$'"${TEMP_LOGS}"`
	local TEMP_JAVA_OPTS=JAVA_OPTS_$name		## jvm参数
	local JAVA_OPTS=`eval echo '$'"${TEMP_JAVA_OPTS}"`
	local TEMP_LISTEN=LISTEN_$name		## 监听日志
	local LISTEN=`eval echo '$'"${TEMP_LISTEN}"`
	local TEMP_SPRING_CONFIG=SPRING_CONFIG_$name	## spring配置参数
	local SPRING_CONFIG=`eval echo '$'"${TEMP_SPRING_CONFIG}"`
	local TEMP_REMOTE=LISTEN_REMOTE_URL_$name	## 远程地址监听
	local REMOTE_URL=`eval echo '$'"${TEMP_REMOTE}"`
	
	if [[ $REMOTE_URL ]];then 
		echo
		echo -n -e "${DEEP_GREEN}正在监听远程地址$REMOTE_URL...${RES}"
		listenRemote "$REMOTE_URL"
		echo -n -e "${DEEP_GREEN}远程地址$REMOTE_URL启动成功，正在启动$name...${DEEP_GREEN}"
	fi
	
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
		rm -f log $LOGS_DIR$log
		nohup java $JAVA_OPTS -jar $SPRING_CONFIG $SERVER_DIR$var_app_name >$LOGS_DIR$log 2>&1 &
		startProcess "$var_app_name" "$LOGS_DIR$log" "$LISTEN"
		rm -f log $LOGS_DIR$OUTPUT_LOGS_NAME
	else
		nohup java $JAVA_OPTS -jar $SPRING_CONFIG $SERVER_DIR$var_app_name > /dev/null 2>&1 &
	fi	
}

## 远程地址监听
function listenRemote(){
	local LISTEN_URL=$1	
	sleep 1
	listen_count=$[$listen_count + 1]
	local listen_num=$[RANDOM%10+1]
	local listen_percent=$[$listen_count + num]
	
	##实时进度百分比
	if [[ $listen_percent -le 99 && ($listen_percent -gt $listen_tempslot || $listen_tempslot -eq 0) ]];then
		listen_tempslot=$listen_percent
		if [ $listen_percent -lt 10 ];then
			printf "%d%%\b\b" $listen_percent
		else
			printf "%d%%\b\b\b" $listen_percent
		fi
	fi
	
	## 特殊服务特殊处理
	if [[ $LISTEN_URL ]];then
		if [[ $(curl -I -s --connect-timeout 1 -m 1 -w "%{http_code}" -o /dev/null $LISTEN_URL) -eq 200 ]];then
			tmsg=200
		fi
	fi

	## 超时退出监听
	if [[ ${tmsg} -ne 200 && $listen_count -eq $TTL ]];then
		echo -e "${RED}$LISTEN_URL 监听超时，请检查该地址是否启动成功！${RES}"
		exit 1
	fi
	
	if [[ ${tmsg} -ne 200 ]];then
		listenRemote "$LISTEN_URL"
	fi
	
	if [[ $listen_percent -ge 100 ]];then
			printf "%d%%\b\b\b" 100
	else
			for(( i=$listen_percent; $i<=100; i++ ))
			do
				sleep 0.01
				if [ $i -lt 10 ];then
					printf "%d%%\b\b" $i
				else
					printf "%d%%\b\b\b" $i
				fi
			done
	fi
	
	listen_tempslot=0
	listen_count=1
	echo
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
			echo -n "$P_INFO" | awk '{printf "%-6s %-8s%-10s %-10s %-10s %-16s %-12s %-12s",$1,$2,$3,$4,$5,$6,$9,$10}'
			echo $SERVER_DIR$name
		else
			echo -e "${RED}$SERVER_DIR$name 服务已关闭${RES}"
		fi
	done
	for ((i=0;$i<${#TOMCAT_ARRAY[*]};i++))
	do
		local name=$(formatParam "${TOMCAT_ARRAY[$i]}")
		local TEMP_TOMCAT_SERVER=TOMCAT_SERVER_$name		##tomcat服务名称
		local TOMCAT_SERVER=`eval echo '$'"${TEMP_TOMCAT_SERVER}"`
		local TEMP_TOMCAT_START=TOMCAT_START_$name	## tomcat启动路径
		local TOMCAT_START=`eval echo '$'"${TEMP_TOMCAT_START}"`
		local P_INFO=`ps -aux|grep "$TOMCAT_SERVER"|grep -v grep|grep -v kill`
		if [[ $P_INFO ]];then
			echo -n "$P_INFO" | awk '{printf "%-6s %-8s%-10s %-10s %-10s %-16s %-12s %-12s",$1,$2,$3,$4,$5,$6,$9,$10}'
			echo "$TOMCAT_START"
		else
			echo -e "${RED}$TOMCAT_SERVER 服务已关闭${RES}"
			
		fi
	done
}

## 检验当前目录是否存在jar包，不存在则报错
function valid(){
	config
	echo -e "${GREEN}正在检测各服务配置...${RES}"
	for ((i=0;$i<${#ALL_ARRAY[*]};i++))
	do
		local aname=$(formatParam "${ALL_ARRAY[$i]}")
		for ((j=0;$j<${#TOMCAT_ARRAY[*]};j++))
			do
			local cname=$(formatParam "${TOMCAT_ARRAY[$i]}")
			if [[ "${cname}" == "${aname}" ]];then
				echo "$cname服务名称重复冲突，请修改配置"
				exit 1
			fi
		done	
	done
	
	local tempArray=(${ALL_ARRAY[*]})
	for ((i=0;$i<${#tempArray[*]};i++))
	do
		local name="${tempArray[$i]}"
		if [ -f  $SERVER_DIR/$name ];then
			echo -e "${GREEN} $name检测成功!${RES}"
		else
			NOTFOUNF=1
			echo -e "${RED}$SERVER_DIR路径下不存在服务：$name ${RES}"
		fi
	done

	for ((i=0;$i<${#TOMCAT_ARRAY[*]};i++))
	do
		local name="${TOMCAT_ARRAY[$i]}"
		local var_app_name=$(formatParam "$name")
		local TEMP_TOMCAT_START=TOMCAT_START_$var_app_name	## tomcat启动路径
		local TOMCAT_START=`eval echo '$'"${TEMP_TOMCAT_START}"`
		local TEMP_TOMCAT_URL=TOMCAT_URL_$var_app_name		##tomcat监听地址
		local TOMCAT_URL=`eval echo '$'"${TEMP_TOMCAT_URL}"`
		local TEMP_TOMCAT_SERVER=TOMCAT_SERVER_$var_app_name  ##tomcat服务名称
		local TOMCAT_SERVER=`eval echo '$'"${TEMP_TOMCAT_SERVER}"`
		validParam "$TOMCAT_SERVER" "$TEMP_TOMCAT_SERVER"
		validParam "$TOMCAT_START" "$TEMP_TOMCAT_START"
		validParam "$TOMCAT_URL" "$TEMP_TOMCAT_URL"
		if [ -f  $TOMCAT_START ];then
			echo -e "${GREEN} $name检测成功!${RES}"
		else
			NOTFOUNF=1
			echo -e "${RED}$SERVER_DIR'路径下不存在服务：$name ${RES}"
		fi
	done
	if [[ $NOTFOUNF -eq 1 ]];then
		exit 1
	fi
	echo -e "${GREEN}服务配置检测完成!${RES}"
}

function config(){
	if [[ "$CONFIG_PATH" == "config" || "${restartType}" == "config" ]];then
		read -ep '请输入服务路径(默认是脚本路径'$CUR_PATH')：' SERVER_DIR
		read -ep '请输入报错重试次数(默认是'$RETRYS')：' TEM_C
		read -ep '请输入超时时间(默认是'$TTL')：' TEM_S
		read -ep '请输入jvm配置(默认无)：' DEFAULT_JAVA_OPTS
	fi
	autoFill
}

function info(){
	echo -e "${BACK_RED}==================默认配置================${RES}"
	echo -e "${BLUE}默认超时(秒):$TTL ${RES}"
	echo -e "${BLUE}默认重试次数:$RETRYS ${RES}"
	echo -e "${BLUE}默认输出日志名称: $OUTPUT_LOGS_NAME ${RES}"
	echo -e "${BLUE}默认监听日志关键内容: $DEFAULT_LISTEN ${RES}"
	echo -e "${BLUE}默认服务路径(当前脚本路径):$SERVER_DIR ${RES}"
	echo -e "${BLUE}默认服务版本号:$SERVER_VERSION ${RES}"
	echo -e "${BACK_RED}==================SERVER配置================${RES}"
	echo -e "${GREEN}SERVER服务:${ALL_ARRAY[*]} ${RES}"
	echo -e "${BLUE}=========================================================== ${RES}"	
	for var_server in ${ALL_ARRAY[*]}
	do
		local var_app_name=$(formatParam "$var_server")
		local TEMP_LOGS=LOGS_$var_app_name		## 日志名称
		local log=`eval echo '$'"${TEMP_LOGS}"`
		local TEMP_JAVA_OPTS=JAVA_OPTS_$var_app_name		## jvm参数
		local JAVA_OPTS=`eval echo '$'"${TEMP_JAVA_OPTS}"`
		local TEMP_LISTEN=LISTEN_$var_app_name		## 监听日志
		local LISTEN=`eval echo '$'"${TEMP_LISTEN}"`
		local TEMP_SPRING_CONFIG=SPRING_CONFIG_$var_app_name	## spring配置参数
		local SPRING_CONFIG=`eval echo '$'"${TEMP_SPRING_CONFIG}"`
		local TEMP_REMOTE=LISTEN_REMOTE_URL_$var_app_name	## 远程地址监听
		local REMOTE_URL=`eval echo '$'"${TEMP_REMOTE}"`
		echo -e "${BLUE}服务名称:$var_server ${RES}"
		echo -e "${BLUE}日志名称:$log ${RES}"
		echo -e "${BLUE}监听日志内容:$LISTEN ${RES}"
		echo -e "${BLUE}jvm参数:$JAVA_OPTS ${RES}"
		echo -e "${BLUE}spring配置参数:$SPRING_CONFIG ${RES}"
		echo -e "${BLUE}远程地址监听:$REMOTE_URL ${RES}"
		echo -e "${BLUE}=========================================================== ${RES}"
	done
	echo -e "${BACK_RED}==================TOMCAT配置================${RES}"
	echo -e "${GREEN}TOMCAT服务:${TOMCAT_ARRAY[*]} ${RES}"
	echo -e "${BLUE}=========================================================== ${RES}"	
	for var_tomcat in ${TOMCAT_ARRAY[*]}
	do
		local var_app_name=$(formatParam "$var_tomcat")
		local TEMP_TOMCAT_START=TOMCAT_START_$var_app_name	## tomcat启动路径
		local TOMCAT_START=`eval echo '$'"${TEMP_TOMCAT_START}"`
		local TEMP_TOMCAT_URL=TOMCAT_URL_$var_app_name		##tomcat监听地址
		local TOMCAT_URL=`eval echo '$'"${TEMP_TOMCAT_URL}"`
		local TEMP_TOMCAT_SERVER=TOMCAT_SERVER_$var_app_name  ##tomcat服务名称
		local TOMCAT_SERVER=`eval echo '$'"${TEMP_TOMCAT_SERVER}"`
		local TEMP_REMOTE=LISTEN_REMOTE_URL_$var_app_name	## 远程地址监听
		local REMOTE_URL=`eval echo '$'"${TEMP_REMOTE}"`
		echo -e "${BLUE}服务名称:$TOMCAT_SERVER ${RES}"
		echo -e "${BLUE}启动路径:$TOMCAT_START ${RES}"
		echo -e "${BLUE}监听地址:$TOMCAT_URL ${RES}"
		echo -e "${BLUE}远程地址监听:$REMOTE_URL ${RES}"		
		echo -e "${BLUE}=========================================================== ${RES}"	
	done
}

## 监控服务心跳
function monitor(){
	if [[ $1 -eq 1 ]];then
		echo 
		sleep 60
		monitor 1
	elif [[ $1 -eq 0 ]];then
		`sed -i '/$0/d' /var/spool/cron/root`
		`crontab -r`
	else
		`*/1 * * * * sh $0 $(start) >> $CUR_PATH"monitor_logs.txt"`
	fi
}

# 格式化数据
function formatParam(){
	local var_app_name=$1
	local param=${var_app_name%%.*} ## 从后面去除后缀最后一个.
	local param=${param%-*}			## 从后面去除后缀第一个-
	local param=${param//-/_}		## 把剩余的—转换为_
	echo $param
}

function initConfig(){
read -ep "请输入SERVER服务数组(空格隔开，禁止逗号)：" ALL_ARRAY
read -ep "请输入TOMCAT服务数组(空格隔开，禁止逗号)：" TOMCAT_ARRAY
CREATEDATE=`date +%c`
echo "## AUTHOR:WINJAY
## DATE:$CREATEDATE
################默认配置##################
##全部包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
##SERVER_VERSION='-1.0.0'
ALL_ARRAY=($ALL_ARRAY)
TOMCAT_ARRAY=($TOMCAT_ARRAY)
##常用包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
##COMMON_ARRAY=('')
##快速包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号,根据最后一个服务来获取关键日志
##FAST_ARRAY=()
##日志默认路径，默认是脚本所在路径
##LOGS_DIR=/opt/da/
##输出日志默认名称，默认output.txt
##OUTPUT_LOGS_NAME=output.txt
##存放服务的默认路径，默认是脚本所在路径
##SERVER_DIR=/opt/da/
##报错重试次数，选填，默认2次
##RETRYS=2
##超时退出，单位秒，选填，默认300秒
##TTL=300
##默认监听日志启动关键内容
##DEFAULT_LISTEN='JVM running for'
################SERVER服务配置【参数选配】##################
##服务命名规则：如da-server-1.0.0.jar，则取名为da_server,注意，必须为下划线
##LOGS_服务名称前缀：日志名称 【选填】
##LISTEN_服务名称前缀：日志监听文本，启动成功的关键内容 【选填】
##JAVA_OPTS_服务名称前缀：服务jvm参数 【选填】
##SPRING_CONFIG_服务名称前缀：spring参数 【选填】
##LISTEN_REMOTE_URL_服务名称前缀：监听远程地址，成功后再往下执行 【选填】
##==============示例 start====================
##ALL_ARRAY=(da-server-1.0.0)
##LISTEN_da_server="Application version is -1: false"
##SPRING_CONFIG_da_server="-Dspring.config.location=/opt/da/prod/,classpath:/application.yml"
##LOGS_da_server=server.txt
##JAVA_OPTS_da_server="-Xdebug -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8887"
##LISTEN_REMOTE_URL_da_server='127.0.0.1:8080'
##==============示例 end======================" > $SCRIPT_CONFIG
for var_server in ${ALL_ARRAY[*]}
	do
		local var_app_name=$(formatParam "$var_server")
		local TEMP_LOGS=LOGS_$var_app_name		## 日志名称
		local TEMP_JAVA_OPTS=JAVA_OPTS_$var_app_name		## jvm参数
		local TEMP_LISTEN=LISTEN_$var_app_name		## 监听日志
		local TEMP_SPRING_CONFIG=SPRING_CONFIG_$var_app_name	## spring配置参数
		local TEMP_REMOTE_URL=LISTEN_REMOTE_URL_$var_app_name		## 监听远程地址
	echo "################$var_server【选填】##################
#$TEMP_LOGS=
#$TEMP_LISTEN=
#$TEMP_JAVA_OPTS=
#$TEMP_SPRING_CONFIG= 
#TEMP_REMOTE_URL= " >> $SCRIPT_CONFIG	　
	done
	
echo "
############Tomcat服务配置【全部参数必须配置】##############
##服务命名规则：如da-server-1.0.0.jar，则取名为da_server,注意，必须为下划线
##TOMCAT_SERVER_服务名称前缀：启动的服务名称，一般说tomcat的名称，用于监听服务【必填】
##TOMCAT_START_服务名称前缀：服务启动路径【必填】
##TOMCAT_URL_服务名称前缀：服务启动成功的监听地址【必填】
##LISTEN_REMOTE_URL_服务名称前缀：监听远程地址，成功后再往下执行 【选填】
##==============示例 start====================
##TOMCAT_ARRAY=(ROOT)
##TOMCAT_SERVER_ROOT='apache-tomcat-9.0.39'
##TOMCAT_START_ROOT='F:/Download/apache-tomcat-9.0.39/bin'
##TOMCAT_URL_ROOT='127.0.0.1:38080'
##==============示例 end======================" >> $SCRIPT_CONFIG	

for var_tomcat in ${TOMCAT_ARRAY[*]}
	do
		local var_app_name=$(formatParam "$var_tomcat")
		local TEMP_TOMCAT_START=TOMCAT_START_$var_app_name	## tomcat启动路径
		local TEMP_TOMCAT_URL=TOMCAT_URL_$var_app_name		##tomcat监听地址
		local TEMP_TOMCAT_SERVER=TOMCAT_SERVER_$var_app_name  ##tomcat服务名称
		local TEMP_REMOTE_URL=LISTEN_REMOTE_URL_$var_app_name		## 监听远程地址
		echo "################$var_tomcat【必填，自行放开注释】##################
#$TEMP_TOMCAT_START=
#$TEMP_TOMCAT_URL=
#$TEMP_TOMCAT_SERVER=
#TEMP_REMOTE_URL= 
" >> $SCRIPT_CONFIG	
	done
	echo "############end此行勿删##############" >> $SCRIPT_CONFIG
	echo -e "${GREEN}已为您创建配置文件$SCRIPT_CONFIG，请自行配置${RES}"
}

if [ ! -f "$SCRIPT_CONFIG" ]; then
	touch "$SCRIPT_CONFIG"
	if [ ! -f "$SCRIPT_CONFIG" ]; then
		echo -e "${RED}$SCRIPT_CONFIG配置文件创建失败，请检查是否有权限创建 ${RES}"
		exit 
	fi
	initConfig
fi

while read line;do
	eval "$line"
done <	$SCRIPT_CONFIG

# 使用 -z 可以判断一个变量是否为空；
function autoFill(){
		if [[ ! $SERVER_DIR ]];then
			SERVER_DIR=$CUR_PATH
		fi
		if [[ "${SERVER_DIR: -1}" != "/" ]];then
			SERVER_DIR=$SERVER_DIR'/'
		fi
		if [[ ! $LOGS_DIR ]];then
			LOGS_DIR=$CUR_PATH
		fi
		if [[ "${LOGS_DIR: -1}" != "/" ]];then
			LOGS_DIR=$LOGS_DIR'/'
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
}			

CONFIG_PATH=$2
autoFill
case "$1" in
	'start')
	valid
	start
	;;
	'stop')
	stop
	;;
	'restart')
	restart
	;;
	'faststart')
	valid
	faststart
	;;
	'status')
	status
	;;
	'info')
	info
	;;
	'java')
	echo "JAVA版本信息："
	java
	;;
	*)
	echo -e "${GREEN}Usage: $0 {|start|stop|restart|faststart|status|info|java}${RES}"
	echo -e "${BACK_RED}==================命令指南================${RES}"
	echo -e "${DEEP_GREEN}start:正常启动所有服务，已经启动的服务不会再次启动，若中途出现报错则会自动重试，重试次数默认2次，服务启动超时则跳过继续下一个服务${RES}"
	echo -e "${DEEP_GREEN}stop:正常关闭所有服务${RES}"
	echo -e "${DEEP_GREEN}restart: 重启服务，需在COMMON_ARRAY定义服务${RES}"
	echo -e "${DEEP_GREEN}faststart: 快速启动所有服务，需在FAST_ARRAY定义服务 ${RES}"
	echo -e "${DEEP_GREEN}status:查看服务状态及基本信息${RES}"
	echo -e "${DEEP_GREEN}info:查看配置文件信息 ${RES}"
	echo -e "${DEEP_GREEN}java:查看java基本信息${RES}"
	exit 1
	;;
esac
```

```txt
##全部包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
SERVER_VERSION='-1.0.0'
ALL_ARRAY=('da-server$SERVER_VERSION')
##常用包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
COMMON_ARRAY=('')
##快速包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号,根据最后一个服务来获取关键日志
FAST_ARRAY=()
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

```shell
$ shc -e 03/31/2007 -m "过期啦~赶紧续费~" -f 2019.sh
gzexe 2019.sh

-e表示脚本将在2007年3月31日前失效, 并根据-m定义的信息返回给终端用户.
```

```shell
## AUTHOR:WINJAY
## DATE:2022年04月25日 星期一 17时16分47秒
################默认配置##################
##全部包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
##SERVER_VERSION='-1.0.0'
ALL_ARRAY=(da-eureka-1.1.0.jar da-config-1.1.0.jar da-zuul-1.1.0.jar da-server-1.1.0.jar da-server-prefiling-1.1.0.jar da-interServer-1.1.0.jar da-rabbitmq-receive-1.1.0.jar)
TOMCAT_ARRAY=(tomcatJT tomcatPRE tomcatTH)
##常用包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号
COMMON_ARRAY=(da-server-1.1.0.jar da-server-prefiling-1.1.0.jar da-interServer-1.1.0.jar da-rabbitmq-receive-1.1.0.jar tomcatJT tomcatPRE tomcatTH)
##快速包,按启动顺序存放所有jar、war包,空格隔开，禁止逗号,根据最后一个服务来获取关键日志
FAST_ARRAY=('da-eureka-1.1.0.jar,da-config-1.1.0.jar' 'da-zuul-1.1.0.jar,da-server-1.1.0.jar,da-server-prefiling-1.1.0.jar,da-interServer-1.1.0.jar' 'da-rabbitmq-receive-1.1.0.jar' 'tomcatJT,tomcatPRE,tomcatTH')
##日志默认路径，默认是脚本所在路径
LOGS_DIR=/data/
##输出日志默认名称，默认output.txt
##OUTPUT_LOGS_NAME=output.txt
##存放服务的默认路径，默认是脚本所在路径
SERVER_DIR=/data/project_release/
##报错重试次数，选填，默认2次
##RETRYS=2
##超时退出，单位秒，选填，默认300秒
##TTL=300
##默认监听日志启动关键内容
##DEFAULT_LISTEN='JVM running for'
################SERVER服务配置【参数选配】##################
##服务命名规则：如da-server-1.0.0.jar，则取名为da_server,注意，必须为下划线
##LOGS_服务名称前缀：日志名称 【选填】
##LISTEN_服务名称前缀：日志监听文本，启动成功的关键内容 【选填】
##JAVA_OPTS_服务名称前缀：服务jvm参数 【选填】
##SPRING_CONFIG_服务名称前缀：spring参数 【选填】
##==============示例 start====================
##ALL_ARRAY=(da-server-1.0.0)
##LISTEN_da_server=Application version is -1: false
##SPRING_CONFIG_da_server=-Dspring.config.location=/opt/da/prod/,classpath:/application.yml
##LOGS_da_server=server.txt
##JAVA_OPTS_da_server=-Xdebug -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8887
##==============示例 end======================
################da-eureka-1.1.0.jar【选填】##################
LOGS_da_eureka='eureka.txt'
#LISTEN_da_eureka=
#JAVA_OPTS_da_eureka=
#SPRING_CONFIG_da_eureka=  　
################da-config-1.1.0.jar【选填】##################
LOGS_da_config='config.txt'
LISTEN_da_config='Application version is -1: false'
#JAVA_OPTS_da_config=
#SPRING_CONFIG_da_config=  　
################da-zuul-1.1.0.jar【选填】##################
LOGS_da_zuul='zuul.txt'
#LISTEN_da_zuul=
#JAVA_OPTS_da_zuul=
#SPRING_CONFIG_da_zuul=  　
################da-server-1.1.0.jar【选填】##################
LOGS_da_server='server.txt'
#LISTEN_da_server=
#JAVA_OPTS_da_server=
#SPRING_CONFIG_da_server=  　
################da-server-prefiling-1.1.0.jar【选填】##################
LOGS_da_server_prefiling='server-pre.txt'
#LISTEN_da_server_prefiling=
#JAVA_OPTS_da_server_prefiling=
#SPRING_CONFIG_da_server_prefiling=  　
################da-interServer-1.1.0.jar【选填】##################
LOGS_da_interServer='inter.txt'
#LISTEN_da_interServer=
#JAVA_OPTS_da_interServer=
#SPRING_CONFIG_da_interServer=  　
################da-rabbitmq-receive-1.1.0.jar【选填】##################
LOGS_da_rabbitmq_receive='rabbit.txt'
#LISTEN_da_rabbitmq_receive=
#JAVA_OPTS_da_rabbitmq_receive=
#SPRING_CONFIG_da_rabbitmq_receive=  　

############Tomcat服务配置【全部参数必须配置】##############
##服务命名规则：如da-server-1.0.0.jar，则取名为da_server,注意，必须为下划线
##TOMCAT_SERVER_服务名称前缀：启动的服务名称，一般说tomcat的名称，用于监听服务【必填】
##TOMCAT_START_服务名称前缀：服务启动路径【必填】
##TOMCAT_URL_服务名称前缀：服务启动成功的监听地址【必填】
##==============示例 start====================
##TOMCAT_ARRAY=(ROOT)
##TOMCAT_SERVER_ROOT='apache-tomcat-9.0.39'
##TOMCAT_START_ROOT='F:/Download/apache-tomcat-9.0.39/bin'
##TOMCAT_URL_ROOT='127.0.0.1:38080'
##==============示例 end======================
################tomcatJT【必填，自行放开注释】##################
TOMCAT_START_tomcatJT='/data/tomcat/apache-tomcat-9.0.50/bin/startup.sh'
TOMCAT_URL_tomcatJT='127.0.0.1:8009'
TOMCAT_SERVER_tomcatJT='apache-tomcat-9.0.50'

################tomcatPRE【必填，自行放开注释】##################
TOMCAT_START_tomcatPRE='/data/tomcat/apache-tomcat-9.0.50-pre/bin/startup.sh'
TOMCAT_URL_tomcatPRE='127.0.0.1:8019'
TOMCAT_SERVER_tomcatPRE='apache-tomcat-9.0.50-pre'

################tomcatTH【必填，自行放开注释】##################
TOMCAT_START_tomcatTH='/data/tomcat/apache-tomcat-9.0.50-th/bin/startup.sh'
TOMCAT_URL_tomcatTH='127.0.0.1:8022'
TOMCAT_SERVER_tomcatTH='apache-tomcat-9.0.50-th'

############end此行勿删##############

```

