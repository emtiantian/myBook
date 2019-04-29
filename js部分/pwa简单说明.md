# pwa 简单说明

## 什么是 pwa

- PWA 全称 Progressive Web App，即渐进式 WEB 应用。
- 本质上是一个网页，通过 2 种新技术可以运行在本地（手机端和桌面端）
  - App Manifest 使网页程序可以在没有记录 url 的使用在桌面上访问，可以实现加载动画。
  - Service Worker 是网页拥有了离线运行的基础，本质是离线缓存资源与请求数据。
  - Service Worker 实现了消息推送

### 大致实现方式

#### 使用 Manifest 添加程序到主屏

`index.html`

```html
<head>
  <title>Minimal PWA</title>
  <meta name="viewport" content="width=device-width, user-scalable=no" />
  <link rel="manifest" href="manifest.json" />
  <link rel="stylesheet" type="text/css" href="main.css" />
  <link rel="icon" href="/e.png" type="image/png" />
</head>
```

`manifest.json`

```json
{
  "name": "Minimal PWA", // 必填 显示的插件名称
  "short_name": "PWA Demo", // 可选  在APP launcher和新的tab页显示，如果没有设置，则使用name
  "description": "The app that helps you understand PWA", //用于描述应用
  "display": "standalone", // 定义开发人员对Web应用程序的首选显示模式。standalone模式会有单独的
  "start_url": "/", // 应用启动时的url
  "theme_color": "#313131", // 桌面图标的背景色
  "background_color": "#313131", // 为web应用程序预定义的背景颜色。在启动web应用程序和加载应用程序的内容之间创建了一个平滑的过渡。
  "icons": [
    // 桌面图标，是一个数组
    {
      "src": "icon/lowres.webp",
      "sizes": "48x48", // 以空格分隔的图片尺寸
      "type": "image/webp" // 帮助userAgent快速排除不支持的类型
    },
    {
      "src": "icon/lowres",
      "sizes": "48x48"
    },
    {
      "src": "icon/hd_hi.ico",
      "sizes": "72x72 96x96 128x128 256x256"
    },
    {
      "src": "icon/hd_hi.svg",
      "sizes": "72x72"
    }
  ]
}
```

#### service worker 简介

- service worker 是 chrome 团队提出的一个 web api,提供可持续的后台处理能力（类似与 axios）
- 特点：
  - 在页面中注册并安装成功后，运行于浏览器后台，不受页面刷新的影响，可以监听和截拦作用域范围内所有页面的 HTTP 请求。
  - 网站必须使用 HTTPS。除了使用本地开发环境调试时(如域名使用 localhost)
  - 运行于浏览器后台，可以控制打开的作用域范围下所有的页面请求
  - 单独的作用域范围，单独的运行环境和执行线程（这个比较重要，不然一直是一个阻塞模式）
  - 不能操作页面 DOM。但可以通过事件机制来处理
  - 事件驱动型服务线程
- 浏览器支持情况
  - chrome 和 firefox 支持 其他基本不支持

### 优缺点

#### 优点

- app 快速开发，桌面全屏运行
- 能够做到部分源生应用功能，可以离线使用
- 消息推送
- 本质是网页，方便更新。（没有原生 app 的各种启动条件，快速响应用户指令）

#### 缺点

- 支持率不高:现在 ios 手机端不支持 pwa，IE 也暂时不支持 （这点就已经让这个技术 pass 了）
- Chrome 在中国桌面版占有率还是不错的，安卓移动端上的占有率却很低
- 各大厂商还未明确支持 pwa （这点就已经让这个技术 pass 了）
- 依赖的 GCM 服务在国内无法使用 （这点就已经让这个技术 pass 了）
- 微信小程序的竞争

#### 尽管有上述的一些缺点，PWA 技术仍然有很多可以使用的点。

- service worker 技术实现离线缓存，可以将一些不经常更改的静态文件放到缓存中，提升用户体验。
- service worker 实现消息推送，使用浏览器推送功能，吸引用户
- 渐进式开发，尽管一些浏览器暂时不支持，可以利用上述技术给使用支持浏览器的用户带来更好的体验。

### 参考链接

[Manifest 参考文档](https://developer.mozilla.org/zh-CN/docs/Web/Manifest)
[chrome 工程师讲解 Manifest](https://developers.google.cn/web/showcase/2015/chrome-dev-summit)
[pwa 详细说明及实现方式](https://segmentfault.com/a/1190000012353473)

### 相关链接

[纸质书籍介绍](https://github.com/SangKa/PWA-Book-CN)
[最佳实践及博客转变模式](https://lzw.me/a/pwa-service-worker.html)
