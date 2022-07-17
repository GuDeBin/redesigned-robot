# git-pages

这个是我想了解的一个技术吧，大概就是自动部署生成 GitHub Pages，我需要他给我的应用每一个做成可以预览的页面

首先了解的是这个[react-flip-demo](https://github.com/MinJieLiu/react-flip-demo)

他使用 git-pages 这个 npm 包，通过 gh-pages -d dist 命令来部署

下面让我先试试

貌似我还是得开开 GitHub io 网站（2022 年 7 月 17 日更新，不是这个问题）

## 解决了

问题点：在于根目录，也就是静态页面部署在 GitHub pages 上时，服务器读取 index.html 时，如果没有设置 homepage 的话，默认部署在根目录下，GitHub Pages 服务器读取按照根目录去寻址，找对应的资源，但是根本找不到的。

之前没有发现问题，我认为是权限的问题，也就是我没有设置什么密钥之类的，无法读取文件

但是在反复使用 gh-pages Github Pages 部署作为关键字去搜寻问题的解决方案，还是在一个博客找到我没有注意的名词，homepage，他也说到不在 package.json 上设置，在构建时会造成静态资源目录以根目录形式存在 index.html 文件中

我用 create-react-app 实验一个后，发现在 build 时提示信息明确说了部署和根目录的信息，还有提供解释和步骤的网址，果然，GitHub pages 部署的详细步骤早就写在官网了

而 vite，也就是我看到的[react-flip-demo](https://github.com/MinJieLiu/react-flip-demo)，他采用的的构建工具，在 vite 的官网上也写了如何在 GitHub Pages 上部署的步骤，只需要在 vit.config.js 上设置 base 默认的仓库名后构建就可以在项目目录下了。

不过，还是 vite 的 base 设置方便点，但是这个我还是更喜欢 React，为了不再路径上过分纠结，尤其是相较下没有过分的优势，就选在以下步骤，[参考文档](https://create-react-app.dev/docs/deployment/)

1. 首先安装 gh-pages npm 包

```shell
npm install --save gh-pages
```

2. 在 package.json 的 script 添加两个指令

```json
  "scripts": {
+   "predeploy": "npm run build",
+   "deploy": "gh-pages -d build",
    "start": "react-scripts start",
    "build": "react-scripts build",
```

这里有一个知识点，pre 在 script 中的意思是 npm script 钩子，每个 npm script 有 pre 和 post 两个钩子, pre 钩子在脚本执行前将被触发, post 则是在脚本执行后触发，[参考文档](https://juejin.cn/post/6917533974285778957)

3. 执行 npm 命令

```shell
npm run deploy
```

4. 在 GitHub 的仓库 setting 中 pages 获取仓库网址，复制到 package.json 中

```json
 "homepage": "https://myusername.github.io/my-app",
```

主要是偷懒，不想手打后面的仓库地址，怕出错，也可以先添加 homepage 在 package.json 中，只需要执行一个 npm run deploy

5. 再次执行 npm run deploy

```shell
npm run deploy
```
