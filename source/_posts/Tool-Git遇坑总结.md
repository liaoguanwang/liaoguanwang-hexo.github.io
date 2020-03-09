---
title: Git遇坑总结
categories:
- Tool
tags:
- Git
date: 2019-09-25 00:28:58
keywors:
description:
top_img:
cover: /img/git.jpg
---

## 【报错信息】fatal: No configured push destination.
```
fatal: No configured push destination.
Either specify the URL from the command-line or configure a remote repository using
```

解决方法
```
这个时候第一次push需要网址：
$ git add --all
$ git commit -m "提交信息"
$ git remote add origin '远程仓库url'
$ git push -u origin 对应远程分支名
 
然后下一次就不用那么麻烦了，直接：
$ git add --all
$ git commit -m "信息"
$ git pus
```

参考：https://blog.csdn.net/COCOLI_BK/article/details/97921497

## 【报错信息】error: failed to push some refs to 'git@github.com:xxx.git'

1、在使用git 对源代码进行push到gitHub时可能会出错

2、出现错误的主要原因是github中的README.md文件不在本地代码目录中

3、可以通过如下命令进行代码合并【注：pull=fetch+merge]

git pull --rebase origin master

4、执行上面代码后可以看到本地代码库中多了README.md文件

5、此时再执行语句 git push 即可完成代码上传到github

参考：
- https://blog.csdn.net/ever69/article/details/97565768
- https://www.cnblogs.com/Tohold/p/9774144.html

## Git rebase
- https://www.jianshu.com/p/f7ed3dd0d2d8

- https://www.centos.bz/2018/01/%E4%BB%8E%E6%92%A4%E9%94%80-rebase-%E8%B0%88%E8%B0%88-git-%E5%8E%9F%E7%90%86/

## 【提示信息】shell显示master|REBASE 1/1)  
解决方法： git rebase --abort

```
If git rebase --continue | --skip | --abort still do not work:

You might try to discard your local commit one by one, then use git status to make sure your local commit are up-to-date with remote branch.

git reset --hard HEAD~1 
NOTE: git reset --hard will discard your work, use it only if you know what you are doing!!
```
参考:
- https://blog.csdn.net/qq_42469247/article/details/88901756
- https://stackoverflow.com/questions/18292578/what-does-git-masterrebase-1-1-mean-how-do-i-get-rid-of-it

## 【报错信心】fatal: refusing to merge unrelated histories
参考：https://blog.csdn.net/u012145252/article/details/80628451
