# Git的使用

### 使用ssh方式将本地项目推送到远程

1.生成公钥

```
$ ssh-keygen -t rsa
```

2.使用命令行创建远程连接ssh：

```ssh
git remote add origin git@github.com:winjayok/repo1.git
```

```
$ git push -u origin master
```

使用命令行clone远程项目

```scheme
$ git clone git@github.com:winjayok/repo3.git
```

