# Docker

## 前言

docker 算是我一直想了解的一个技术，原因在于它宣称的技术，容器化，部署不再考虑环境，一个比虚拟机更加通用且节省资源的技术。

- 部署方便
- 节省资源

看见这两个技术两点后，我就再也没能忘了这个，这个小骚货，可一直挠着我的心

## 历史

等我有空再补充

## 安装

这次直接使用阿里云的安装教程

```sh
# step 1: 安装必要的一些系统工具
sudo apt-get update
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# step 2: 安装GPG证书
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# Step 3: 写入软件源信息
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# Step 4: 更新并安装Docker-CE
sudo apt-get -y update
sudo apt-get -y install docker-ce

# 安装指定版本的Docker-CE:
# Step 1: 查找Docker-CE的版本:
# apt-cache madison docker-ce
#   docker-ce | 17.03.1~ce-0~ubuntu-xenial | https://mirrors.aliyun.com/docker-ce/linux/ubuntu xenial/stable amd64 Packages
#   docker-ce | 17.03.0~ce-0~ubuntu-xenial | https://mirrors.aliyun.com/docker-ce/linux/ubuntu xenial/stable amd64 Packages
# Step 2: 安装指定版本的Docker-CE: (VERSION例如上面的17.03.1~ce-0~ubuntu-xenial)
# sudo apt-get -y install docker-ce=[VERSION]


```

无语

云服务器上正常，到了这个什么 WSL 上就。。。。。。出问题了

还给我一个警告

```sh
W: https://mirrors.aliyun.com/docker-ce/linux/ubuntu/dists/jammy/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details.
```

好样的

我真的是好样的

虽然解决了，但是，我。。。。回头再写一篇综合的文章介绍下吧

## Microsoft Learn

这是我在 Microsoft Learn 的 docker 教程

现在的团队必须快速发布应用，以吸引和留住客户。 由于存在这种要求，软件开发和支持团队必须始终考虑可节省时间和成本的解决方案。 理想的解决方案将减少创建和配置部署环境所花费的时间，并简化软件部署过程。

普遍认为可以将软件容器化技术用作节省时间和降低成本的解决方案。 容器化的一项优势是，无需配置硬件和花费时间安装操作系统和软件来托管部署。 容器之间彼此隔离，并且多个容器可以在相同硬件上运行。 此配置可帮助我们更加高效地使用硬件，并且可以帮助提升应用程序的安全性。

假设你就职于一家在线服装零售公司，你们公司计划开发一些内部应用。 你的团队在本地开发和测试所有应用程序，然后将其部署到 Azure 进行预生产测试和最终生产托管。 你希望在进行很少配置更改或不进行任何配置更改的情况下在每个环境中实现最大兼容性。 使用 Docker 作为容器化解决方案似乎是理想的选择。

在这里，你将了解如何使用 Docker 创建自己的容器。 你还将大致了解 Docker 基础结构的幕后工作原理。 目标是帮助你确定 Docker 容器是否适合你的业务。

学习目标

通过学习本模块，你将获得

- 评估 Docker 是否为合适的容器化平台
- 介绍 Docker 容器的组件如何支持计算容器实现

先决条件

- 操作系统虚拟化概念的基础知识
- 基于命令行的应用程序的基础知识

什么是 Docker

在开始 Docker 容器快速导览之前，先了解我们的团队如何开发和部署应用程序。 我们还将简要介绍团队所面临的一些挑战。

在公司开发和管理应用程序的过程中，通常涉及一个或多个团队。 开发团队创建软件，运营团队负责部署这些应用程序。 此外，运营团队还负责管理应用程序托管基础结构。

例如，假设我们要开发一个订单跟踪门户，供公司的各个专卖店使用。 在应用的开发和发布过程中，将由多个环境托管我们的应用程序。 首先，开发团队在开发环境中开发和测试软件。 然后，会将该软件部署到质量保证 (QA) 环境、预生产环境和最终生产环境。

**复制粘贴结束**

稍后再继续
