# UmiJS

中文叫做乌米，蚂蚁出品，属于蚂蚁自己的框架，虽然在知乎上貌似路灯侠并不喜欢，但是能将自己的内部框架放出来供大家使用，这在国内互联网企业里本就属于异类，关键还不是免费流量，价值收割一套。

说真的，目前也就是百度为了普及自己的飞浆而大规模开源想笼络住下一个时代的开发者的习惯，但是蚂蚁的开源，我真的还没观察到除了分享外的其他想法

这里再说一遍国内开发者的奇葩脑回路，真正的开源是将自己的想分享的代码分享出去，而不是满足市场需要制造满足，虽说为了普及，会考虑众多开发者的体验，但那是为了更好的分享，而不是满足。

自我感受先说完，开始我的体验之旅

## 官网教程

一个 blog

先看完这个教程，只能说开发者们还是希望使用者是一个有一定经验，至少是编写过一个应用的开发者，也就是

我。。。。。惨被淘汰

在希望读者自己完成的章节中，我只有基本的逻辑，至于如何实现实在是一篇空白，果然是一个 HelloWorld 仔

还是多编写代码

## examples

ant-design-pro

UmiJS

UmiMax

三者的关系

我的理解是 UmiJS 是底座，是一个在 react 延申出的一整套技术框架，有着自己对前端理解和日常业务中逻辑的抽离后形成一个框架，与 NextJS 在同一个层面，他有约束，但是并不是决定的

我应该说的简单点，在 react 的生态圈里诞生里 UmiJS，在 UmiJS 的实践中摸索出 UmiMax，然后在 UmiJS 和 ant design 的基础上诞生了最佳实践 Ant design pro 这个中后台前端/设计

也就是说，ant-design-pro 是中后台的最佳实践，而 UmiMax 是 Umi 前端的最佳实践，感觉 UmiMax 比 ant-design-pro 使用的范围更加全面，UmiJS 是这一切的基础

## 有个想法

我之前 fork 鱼皮的前端面试鸭，他是以 ant-design-pro 为基座做的，其实应该是删除了大部分中后台的示例代码，因为面试鸭是面向客户端的，不可能采用导航页面，我是不是可以用 UmiMax 来重写这个网站，并作些其他的改动呢

还有，我对比了几个 CLI 工具生成的基本框架，分别是

1. ant-design-pro umi3 simple
2. ant-design-pro umi3 complete
3. ant-design-pro umi4
4. umi simple
5. Umi@Max Ant Design Pro

这五个有点奇特，完整的组件页面只有第二个，但是 npm 依赖已经安装不上去了，但是我可以用其他的，也就是其他的 simple 来搭起来一个完整的后台框架

但是我发现在 UmiJS 的 GitHub 上的 example 也是有个一例子，我有点不知道怎么抉择，还是将 UmiJS 整个下载到本地

## UmiJS-MAX

先来描述下官网的定义

> Umi 作为一个可扩展的企业级前端应用框架，在蚂蚁集团内部已经已直接或间接地服务了 10000+ 应用。在工程实践的过程中，解决大量前端开发中开发中遇到的常见问题，这些经验累积成 Umi 各个插件。为了方便开发者更加方便的使用这些插件，在我们这些插件开源的基础上，直接将他们集成到一起，打造了 @umijs/max。 让开发者直接可以通过脚手架马上获得和蚂蚁集团开发 Umi 应用一样的开发体检。

也就是说这个是在 UmiJS 的基础之上，集合一些常用的插件，插件涉及一些

- 权限
- 站点统计
- Antd
- dva
- initial-state
- 布局和菜单
- 多语言
- model
- 乾坤微前端
- 请求库
- TailwindCSS 支持
- Moment2dayjs
- Module Federation

权限、站点统计、Antd 这些我知道

但是 dva，只知道是一个简化的状态处理，类似 redux 的状态工作流，也就是单向数据流方式，因为只是写过一个 demo，并不清楚

initial-state，运行时配置，这个配置是应用在浏览器端的，可以配置很多东西，dva、数据流之类的，在文章的开头说到约定 src/app.tsx 为运行时配置，那么.umirc.ts 是属于什么配置，还是只是配置。

布局和菜单、多语言（也就是国际化）也是属于配置类的，通过配置来实现

model 也就是可以作为简易数据来代替 dva

请求库，基于 axios 和 ahooks 的 useRequest，基本是也是请求和响应的配置，不得不说，蚂蚁作为应用方，将规范下放到底，对每一个细节都有自己的规范。基本上就是在队列行军，排队枪毙。很适合我这种整天想跑的兵油子。

Tailwind CSS 插件，这个就是集成支持 Tailwind CSS 的插件，可是 AntD 明明选择了 CSS-in-JS，这个意思是 UmiJS 觉得原子化 CSS 才是王道，是因为 Facebook 选择了原子化 CSS。

moment2dayjs 插件，按照描述，只是用来替换 momentJs 的，UmiJS 只是将自己的配置再次转化为相关插件的配置吗

Module Federation 插件，这个是 webpack5 推出的在应用程序之间共享代码的新方法，这个我还真的不知道怎么回事

再看看这个脚手架生成的 Demo

它是一个建议的后台样式，左侧的侧边栏，右侧是主要内容。

先看看他的 src 文件

## 继续

UmiJs 的插件是做什么用的呢

学习的姿势貌似不对啊

国际化貌似不行啊

```js
import { useIntl } from "@umijs/max";
```

无法导出这个，好像这里面就没有相应的插件

并不是不存在插件，而是不熟悉 umi 的机制，配置

先说下解决方式

UmiMax

这个需要在配置文件，.umirc.ts 中新增

```js
export default defineConfig({
  locale: {},
});
```

在启动 pnpm dev 后，umi 应该是读取到配置文件更改，自动在 src/.umi 文件下生成对应插件的文件，而系统也会将相应的插件按需读取到系统中

而 Simple App，也就是我定义的 umi 则需要两个步骤

```sh
pnpm i @umijs/plugins
```

在配置文件.umirc.ts 中新增两条

```js
export default defineConfig({
  plugins: ["@umijs/plugins/dist/locale"],
  locale: {},
});
```

在启动 pnpm dev 后如同 UmiMax 一样，按需开启插件

这也算一种按需读取和配置文件的方式，一切从 umi 开启

不过，多个插件呢

解决了，直接在 plugins 后的数组添加地址

```js
plugins: ["@umijs/plugins/dist/locale", "@umijs/plugins/dist/antd"],
```

有一个插曲，那就是在没有安装 antd 时，开启 antd 插件，被识别出来，直接提示错误

这是不是意味着，必须在 dev 模式下开发呢
