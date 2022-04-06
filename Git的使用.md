# Git的使用

### Git安装及配置

git安装及配置：![img](G:\markdown\typora-user-images\8LDO48C$8@[GWU0353$FOVS.png)https://zhuanlan.zhihu.com/p/123195804
git代码拉取及上传配置：https://blog.csdn.net/weixin_45272449/article/details/106358069

### 使用ssh方式将本地项目推送到远程

1.生成ssh公钥

```shell
$ ssh-keygen -t rsa
```

2.使用命令把文件推送到远程：

```shell
git add . #将当前目录下的文件加入到仓库
git commit  -m  "备注"  #提交并添加备注
git remote add origin1 git@github.com:winjayok/repo2.git #添加一个origin1
$ git push -u origin1 master #推送到远程
```

3.使用命令删除远程文件

```shell
git rm test.txt  #选择要删除的文件
tm 'test.txt'
git commit  -m  "删除"  #提交并添加备注
$ git push -u origin1 master #正常推送到远程
$ git push -f origin1 master #强制推送到远程
#放弃本地项目，强制更新
git fetch --all
git reset --hard origin/master
git pull //可以省略
```

4.使用命令行clone远程项目

```shell
$ git clone git@github.com:winjayok/supermarket.git
```

5.命令大全

```shell
git branch 查看本地所有分支
git status 查看当前状态
git commit 提交
git branch -a 查看所有的分支
git branch -r 查看远程所有分支
git commit -am "init" 提交并且加注释
git remote add origin git@192.168.1.119:ndshow
git push origin master 将文件给推到服务器上
git remote show origin 显示远程库origin里的资源
git push origin master:develop
git push origin master:hb-dev 将本地库与服务器上的库进行关联
git checkout --track origin/dev 切换到远程dev分支
git branch -D master develop 删除本地库develop
git checkout -b dev 建立一个新的本地分支dev
git merge origin/dev 将分支dev与当前分支进行合并
git checkout dev 切换到本地dev分支
git remote show 查看远程库
git add .
git rm 文件名(包括路径) 从git中删除指定文件
git clone git://github.com/schacon/grit.git 从服务器上将代码给拉下来
git config --list 看所有用户
git ls-files 看已经被提交的
git rm [file name] 删除一个文件
git commit -a 提交当前repos的所有的改变
git add [file name] 添加一个文件到git index
git commit -v 当你用－v参数的时候可以看commit的差异
git commit -m "This is the message describing the commit" 添加commit信息
git commit -a -a是代表add，把所有的change加到git index里然后再commit
git commit -a -v 一般提交命令
git log 看你commit的日志
git diff 查看尚未暂存的更新
git rm a.a 移除文件(从暂存区和工作区中删除)
git rm --cached a.a 移除文件(只从暂存区中删除)
git commit -m "remove" 移除文件(从Git中删除)
git rm -f a.a 强行移除修改后文件(从暂存区和工作区中删除)
git diff --cached 或 $ git diff --staged 查看尚未提交的更新
git stash push 将文件给push到一个临时空间中
git stash pop 将文件从临时空间pop下来
---------------------------------------------------------
git remote add origin git@github.com:username/Hello-World.git
git push origin master 将本地项目给提交到服务器中
-----------------------------------------------------------
git pull 本地与服务器端同步
-----------------------------------------------------------------
git push (远程仓库名) (分支名) 将本地分支推送到服务器上去。
git push origin serverfix:awesomebranch
------------------------------------------------------------------
git fetch 相当于是从远程获取最新版本到本地，不会自动merge
git commit -a -m "log_message" (-a是提交所有改动，-m是加入log信息) 本地修改同步至服务器端 ：
git branch branch_0.1 master 从主分支master创建branch_0.1分支
git branch -m branch_0.1 branch_1.0 将branch_0.1重命名为branch_1.0
git checkout branch_1.0/master 切换到branch_1.0/master分支
du -hs

git branch 删除远程branch
git push origin :branch_remote_name
git branch -r -d branch_remote_name
```

## git 通用添加忽略提交名单

先在根目录新增一个.gitignore文件

/target/ ：过滤文件设置，表示过滤这个文件夹
*.mdb  ，*.ldb  ，*.sln 表示过滤某种类型的文件
/mtk/do.c ，/mtk/if.h  表示指定过滤某个文件下具体文件
 !*.c , !/dir/subdir/     !开头表示不过滤
 *.[oa]    支持通配符：过滤repo中所有以.o或者.a为扩展名的文件

其他的一些过滤条件

  \* ？：代表任意的一个字符
  \* ＊：代表任意数目的字符
  \* {!ab}：必须不是此类型
  \* {ab,bb,cx}：代表ab,bb,cx中任一类型即可
  \* [abc]：代表a,b,c中任一字符即可

  \* [ ^abc]：代表必须不是a,b,c中任一字符

  由于git不会加入空目录，所以下面做法会导致tmp不会存在 tmp/*       //忽略tmp文件夹所有文件

  改下方法，在tmp下也加一个.gitignore,内容为
            *
            !.gitignore

```shell
##ignore this file##
*/target/
*/src/test/
*.idea
.classpath
.project
.setting
*.iml
*.sql
*.bak
 
```

