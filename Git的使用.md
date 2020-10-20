# Git的使用

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
$ git push -u origin1 master #推送到远程
```

4.使用命令行clone远程项目

```shell
$ git clone git@github.com:winjayok/repo3.git
```

