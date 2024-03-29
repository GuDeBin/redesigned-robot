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
# 这个是一个赋值与变量path
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
# 这是一个case in esac，相当于条件分支选择

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
# 上述是git推送流程，先添加到暂存区，在提交到仓库，最后推送到远程仓库
```

```shell

path='项目路径'

cd ${path}

printf "进入项目\n"

git add .

printf '提交暂存区\n'

git commit -m 'update 2022年7月15日'

printf '提交仓库\n'

git push origin main

printf '推送远程仓库\n'

printf 'end'
```

这个可以做成函数

而判断是否需要调用一下

坑：这个 shell 各个字符串和命令绝不能连一起，这个是真的是，shell 不会是用遍历字符串来执行命令吧

新知识

1. 管道符 \| 这个意思是上一个上一个命令的输出是下一个命令的输入，一个衔接作用。

2. grep

还是用=~

## 2022 年 7 月 19 日

这些天有些事情耽误了，今天晚上就得搞定它

shell 脚本经过这些天的了解，还是比一般的编程语言简单点，当然入门简单，后续要是做些更复杂的任务还是需要学习更多

首先基本构造

一个数组放置需要推送到 GitHub 上的项目文件路径

一个循环处理

循环调用一个函数

函数的功能

1. 进入目录，根据 git status 返回的状态判断是否需要执行添加到暂存、提交和推送命令

刚刚有一个想法，我或许不应该用函数执行，这个有点，大材小用了

## 开始

1. 获取相应的目录

2. 函数遇到问题，我无法传入循环的参数？，问题解决：原因是 shell 的函数必须在程序调用前定义好，否则无法调用

3. 又遇到一个问题，我如何将 git status 的状态提取出来，现在可以使用 git status -s 命令获取简短的输出结果，也就是如果没有变动，则没有任何输出，问题解决：使用反引号，也就是``，这个类似 JS 的字符串模板。在反引号里可以执行 shell 命令，我是用的是

```shell
check_push=`git status | grep "nothing to commit, working tree clean"`
```

| 运算符是可以将上一个命令的输出当作右侧命令的输入，也就是用 grep 来检查字符串是否包含 nothing to commit, working tree clean，包含意味着项目没有变动

4. 后面遇到一个是提交指定的信息，git commit -m “Site updated：2022-07-22 14:30:00”，这种类似的信息，需要拼接字符串和 shell date 命令来获取系统的时间，但是需要格式其中包含的知识点有这几个：拼接字符串需要用双引号，反引号可以执行 shell 命令，但是需要格式化

```shell
git commit -m "`date +"Site updated: %Y-%m-%d %H:%M:%S"`"
```

## 最后完成

```shell
# 需要推送的项目文件目录

my_list_project[0]='/home/gdb/gdb/project'
# 需要遍历的项目文件可以一直加
# my_list_project[0]='/home/gdb/gdb/project-2'

# git-push函数
git_push(){
    # 进入指定目录
    cd $1
    printf "进入 $1 目录\n"

    check_push=`git status | grep "nothing to commit, working tree clean"`

    # echo $check_push
    if [[ $check_push =~ "nothing to commit, working tree clean" ]]
    then
        echo "无需提交"
    else
        echo "需要提交，开始执行"
        # 执行添加、提交和推送
        git add .
        git commit -m "`date +"Site updated: %Y-%m-%d %H:%M:%S"`"
        git push origin main
    fi

    echo "休息一秒"
    sleep 1s
    echo "休息结束"

}

# 循环处理
for temp in ${my_list_project[@]}
do
    git_push ${temp}
done

```
