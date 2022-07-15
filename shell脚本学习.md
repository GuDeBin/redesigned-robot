# shell 脚本学习

## 先说下缘由

我每次写的各种文档都需要一个个点击的提交更改、在推送到 GitHub 上，这个操作其实没什么需要特殊的技巧

只是三个命令

1. 检查是否有项目文件下是否有更改

2. git 提交并附带信息，一般是 update 当天的日期

3. 推送到 GitHub 山

## 我的思路

大致上就是利用 shell 脚本，批量执行上述步骤

## 问题

1. 我不清楚 shell 脚本的编写规则

## 执行

先开始对 shell 了解下

概括下

摘自菜鸟教程

Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。

Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

终端命令 echo $SHELL 可以查看当前默认的 shell 解释器

我的是 bash，大多数发行版的 Linux 默认的 shell 解释器

先明白定义，再看看作用

其实在理解的编程语言后，可以将 shell 看作一个编程语言，而脚本的意思是批量执行

我的理解是将两条及以上的终端命令放入一个文件进行执行，也就是未来方便执行多数命令，但是 shell 拥有编程语言的特性，有变量、判断和循环执行的能力

而我的需求，遍历项目文件夹、判断是否有变动的文件，如果是执行 git 提交与推送命令

## 经历与坑

一个简单的文件 hello.sh

```shell
#!/bin/bash
echo "Hello World !"
```

但是出现一个情况，就是权限，三种执行 shell 脚本的方式

1. bash 也就是解释器执行，但是这个好像是生成一个子解释器，在这个子解释器的域里执行，也就相当于新开一个进程去处理，和当前环境有隔离

2. 文件路径执行，直接在终端输入文件的绝对或者相对路径，但是需要提前给与文件执行权限

3. source 或者 . 的方式执行，这个是可以在当前的环境下执行，也就是我之前看到的科普视频推荐的方式，他的理由是解释器执行由于和当前环境有隔离，所以可能存在不可知的问题（我估计是他遇到这类问题，无法找到原因，但是换一个执行方式，就可以，可行路径为默认路径）

目前是选择用 bash 解释器方式执行，这是因为我看到的都是 bash 解释器执行，在这个上面深究对当下的我没有实际意义

## 实操

首先是看看他人的脚本是如何编写的

先解析一个 git 脚本

```shell

if [ ! $1 ]
then
  echo "####### 请输入自己的项目 #######"
  exit;
fi
# 解释 这是一个流程控制，if then ，其中exit属于退出当前进程，也就是退出的意思，而fi属于if的倒写，表示if结束
# $1表示执行命令时输入的参数，从一开始，零是执行文件名包含地址
# 这倒也解释了为什么再用脚手架时是参数输入在命令后面
if [ ! $2 ]
then
  echo "####### 请输入commit值 #######"
  exit;
fi

#给出一个默认的项目路径
path="/Users/duodian/Desktop/work/miniProgram"

case "$1" in
  "p")
  echo "PC项目"
  path="/Users/duodian/Desktop/work/web-pc"
  ;;
  "m")
  echo "小程序项目"
  path="/Users/duodian/Desktop/work/miniProgram"
  ;;
  "h")
  echo "H5项目"
  path="/Users/duodian/Desktop/work/web-h5"
  ;;
  "c")
  echo "后台项目"
  path="/Users/duodian/Desktop/work/circleManAdmin"
  ;;
esac

#先进入项目当中

cd $path

echo "####### 进入自己的项目 #######"

ls

echo "开始执行命令"

git add .

git status

#写个sleep 1s 是为了解决并发导致卡壳

sleep 1s

echo "####### 添加文件 #######"

git commit -m "$2"

echo "####### commit #######"

sleep 1s

echo "####### 开始推送 #######"

if [ ! $3 ]
then
  echo "####### 请输入自己提交代码的分支 #######"
  exit;
fi

git push origin "$3"

echo "####### 推送成功 #######"
```
