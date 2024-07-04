# 为什么不用 Iframe

为什么不用 iframe，这几乎是所有微前端方案第一个会被 challenge 的问题。但是大部分微前端方案又不约而同放弃了 iframe 方案，自然是有原因的，并不是为了 "炫技" 或者刻意追求 "特立独行"。

如果不考虑体验问题，iframe 几乎是最完美的微前端解决方案了。

iframe 最大的特性就是提供了浏览器原生的硬隔离方案，不论是样式隔离、js 隔离这类问题统统都能被完美解决。但他的最大问题也在于他的隔离性无法被突破，导致应用间上下文无法被共享，随之带来的开发体验、产品体验的问题。

1. url 不同步。浏览器刷新 iframe url 状态丢失、后退前进按钮无法使用。
2. UI 不同步，DOM 结构不共享。想象一下屏幕右下角 1/4 的 iframe 里来一个带遮罩层的弹框，同时我们要求这个弹框要浏览器居中显示，还要浏览器 resize 时自动居中..
3. 全局上下文完全隔离，内存变量不共享。iframe 内外系统的通信、数据同步等需求，主应用的 cookie 要透传到根域名都不同的子应用中实现免登效果。
4. 慢。每次子应用进入都是一次浏览器上下文重建、资源重新加载的过程。

其中有的问题比较好解决(问题 1)，有的问题我们可以睁一只眼闭一只眼(问题 4)，但有的问题我们则很难解决(问题 3)甚至无法解决(问题 2)，而这些无法解决的问题恰恰又会给产品带来非常严重的体验问题， 最终导致我们舍弃了 iframe 方案。

# qiankun

基于 single-spa 实现的微前端框架

## qiankun 优点

- 接入方式简单，HTML Entry 方式接入
- 支持同一页面多个应用，但是要注意路由激活规则，每个子应用的激活路由不能相同，有路由会 404
- 样式隔离, strictStyleIsolation 基于 shadow DOM
- js 沙箱
- 支持预加载
- 兼容 ie，解决浏览器不支持 Proxy，但是不支持多应用场景

- 加载微应用例子

```javascript
import { loadMicroApp } from "qiankun";

/** 手动加载一个微应用 */
const microApp = loadMicroApp({
  name: "microApp",
  entry: "https://localhost:7001/micro-app.html",
  container: "#microApp",
  activeRule: "/child",
});

// 手动卸载
microApp.mountPromise.then(() => microApp.unmount());
```

- 如何部署
  主应用和子应用都放在同一个服务器，需要做好 webpack 打包 的配置

[乾坤官网](https://qiankun.umijs.org/zh/cookbook#%E5%90%8C%E6%97%B6%E5%AD%98%E5%9C%A8%E5%A4%9A%E4%B8%AA%E5%BE%AE%E5%BA%94%E7%94%A8%E6%97%B6)
[羽雀](https://www.yuque.com/kuitos/gky7yw/viueoh#ecf5750b)

## qiankun 缺点

- 不支持 keep-alive

# 无界

基于 WebComponent 容器 + iframe 沙箱. 无界对子应用的侵入非常小

解决了 iframe 三个难题

- 路由状态丢失，刷新一下，iframe 的 url 状态就丢失了
- dom 割裂严重，弹窗只能在 iframe 内部展示，无法覆盖全局
- 通信非常困难，只能通过 postmessage 传递序列化的消息

## 优势

- 多应用同时激活在线

- 框架具备同时激活多应用，并保持这些应用路由同步的能力

- 组件式的使用方式 无需注册，更无需路由适配，在组件内使用，跟随组件装载、卸载

- 应用级别的 keep-alive

- 子应用开启保活模式后，应用发生切换时整个子应用的状态可以保存下来不丢失，结合预执行模式可以获得类似 ssr 的打开体验

- 纯净无污染
  无界利用 iframe 和 webcomponent 来搭建天然的 js 隔离沙箱和 css 隔离沙箱
  利用 iframe 的 history 和主应用的 history 在同一个 top-level browsing context 来搭建天然的路由同步机制
  副作用局限在沙箱内部，子应用切换无需任何清理工作，没有额外的切换成本
  性能和体积兼具

[无界官方网站](https://wujie-micro.github.io/doc/)

# micro-app

借鉴 WebComponent 思想，通过自定义元素结合自定义 ShadowDom，将微前端封装成类 WebComponent 组件

优点： 不需要修改 webpack 配置，接入简单

# webpack 模块联邦

由于需要保持库版本一致性，依赖性比较强，不支持增量替换，排除此方案，没有样式隔离

# pnpm + workspace + changesets

npm 存在的问题

- 不同包之间共同依赖的包会提升到顶层，如果版本不同，各个包会安装各自的版本
- 存在幽灵以来问题

pnpm 安装速度快、节省磁盘空间、避免幽灵依赖，内置对 monorepo 的支持，

changesets 管理 monorepo 项目下的版本以及 changeLog、包的发布

changesets 工作流

- pnpm changeset init, 在根目录下生成.changeset 目录，其中 config.json 是默认配置文件,配置如下

  ```json
  {
    "$schema": "https://unpkg.com/@changesets/config@2.0.0/schema.json",
    "changelog": "@changesets/cli/changelog",
    "commit": false,
    "linked": [["@qftjs/*"]],
    "access": "public",
    "baseBranch": "main",
    "updateInternalDependencies": "patch",
    "ignore": [],
    "___experimentalUnsafeOptions_WILL_CHANGE_IN_PATCH": {
      "onlyUpdatePeerDependentsWhenOutOfRange": true
    }
  }
  ```

  changelog: changelog 生成方式
  commit: 不要让 changeset 在 publish 的时候帮我们做 git add
  linked: 配置哪些包要共享版本
  access: 公私有安全设定，内网建议 restricted ，开源使用 public
  baseBranch: 项目主分支
  updateInternalDependencies: 确保某包依赖的包发生 upgrade，该包也要发生 version upgrade 的衡量单位（量级）
  ignore: 不需要变动 version 的包
  \_\_\_experimentalUnsafeOptions_WILL_CHANGE_IN_PATCH: 在每次 version 变动时一定无理由 patch 抬升依赖他的那些包的版本，防止陷入 major 优先的未更新问题

- 运行打包命令

  ```json
  {
    "build": "pnpm --filter=@qftjs/* run build"
  }
  ```

- 运行 changeset，将包列出来，选择要更改发布的包

```json
{
  "changeset": "changeset"
}
```

- changeset version 修改发布包的版本
- changeset publish

可以在 CI actions 中使用
