# React 动画实现

项目来自[react-flip-demo](https://github.com/MinJieLiu/react-flip-demo)

## 开始

首先先说下这个是什么，这是一个 react 动画实现的 demo，觉得很有趣，刨析下

首先看下项目的文件结构

插下，这个项目文件目录是用 mddir 这个 npm 包实现的，简单来说是如下操作

1. 首先是安装

```shell
npm install mddir -g
```

2. cd 到项目目录，并执行 mddir

```shell
cd 项目目录

mddir
```

3. 在项目目录下就会生成一个 directoryList 的 Markdown 文件，内容就是项目文件目录

注意事项

1. mddir 会自动忽视 node_modules 和.git 文件夹

2. 在复制到文档中时，需要用三个反引号包裹，就像 markdown 中输入程序段落一样，不然就是一段乱码

## 回到正题

该项目的文件目录如下

```
|-- project
    |-- .gitignore # git排除文件
    |-- LICENSE # 许可证
    |-- README.md # 自述文件
    |-- index.html # 起始文件
    |-- package-lock.json # npm包
    |-- package.json # npm文件
    |-- tsconfig.json # typescript配置文件
    |-- tsconfig.node.json #项目引用
    |-- vite.config.ts # vite配置
    |-- src # 源码
        |-- App.module.css # app基本样式
        |-- App.tsx # app起始文件
        |-- index.css # 基本样式
        |-- main.tsx # 起始文件
        |-- vite-env.d.ts # vite环境配置
        |-- components # 组件文件夹
        |   |-- Flipper # 过度文件，组件
        |       |-- FlipContext.ts
        |       |-- Flipped.tsx
        |       |-- Flipper.tsx
        |       |-- index.ts
        |-- pages # 页面
            |-- Demo1.tsx # Demo1
            |-- Demo2.tsx # Demo2
            |-- Demo3.tsx # Demo3
            |-- utils.ts # 通用工具文件
            |-- components # 页面组件
            |   |-- doc-components.tsx # 基础的样式组件
            |-- hooks
                |-- useSortProperty.tsx #  封装react-dnd动画的hook库
```

### 其中用到的 npm 包

#### styled-components

这个是关于 CSS 样式，通过看文档和实例，我总结是将关注于全局的 CSS 压缩到关注单个组件的范围内，也就是将关注点放在当下的事情上。隔断 CSS 全局，更加灵活的使用 css

####
