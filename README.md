## 美信拓扑 IM uniapp 版

[美信拓扑](https://www.maximtop.com/)，一键启用多云架构的即时通讯云服务

美信拓扑 IM 为美信拓扑云服务的 DemoApp，方便 App 开发者体验和使用 IM SDK，可以直接[在线试用](https://chat-h5.maximtop.com)，也可以在官网[下载页面](https://www.maximtop.com/downloads/)选择试用所有客户端。

[![Scc Count Badge](https://sloc.xyz/github/maxim-top/maxim-uniapp/?category=total&avg-wage=1)](https://github.com/maxim-top/maxim-uniapp/) [![Scc Count Badge](https://sloc.xyz/github/maxim-top/maxim-uniapp/?category=code&avg-wage=1)](https://github.com/maxim-top/maxim-uniapp/)

## 工程说明

1. 本工程为 Uniapp 工程，实际上是从[微信小程序](https://github.com/maxim-top/maxim-miniprogram)转换而来，感谢[zhangdaren](https://github.com/zhangdaren/miniprogram-to-uniapp)，转换过程非常顺畅；
2. 推荐使用此版本进行小程序/H5 等版本开发，感谢 [DCloud.io](https://dcloud.io) 开发这么好的框架；
3. DemoApp 是为了演示 IM SDK 调用而开发，也因此最好的开发方式为根据 DemoApp 找到功能，然后直接查看使用示例；
4. 本工程 DemoApp 不包含所有功能的演示，但是 SDK(floo) 功能完全，高级功能可以参照[PC Web 版本](https://github.com/maxim-top/maxim-web)，SDK 调用方式是通用的。

此工程共有四个源码目录：

1. im 存放美信拓扑 IM SDK，当前最新版本为 floo-2.0.0.uniapp.js
2. pages 为 UI 源码目录；
3. utils 为使用的工具类源码；
4. third 为第三方源码；

## 准备工作

1. 运行命令安装依赖包

> `npm install`

2. 美信拓扑 AppID

打开文件 App.vue, 修改变量 appid，将 "welovemaxim" 改为你的应用 AppID，此 AppID 为在[美信拓扑后台](https://console.maximtop.com/)创建应用后获取。

3. 如果开发小程序，还需修改对应小程序平台的 AppID

如微信小程序，可以打开文件 `manifest.json`，修改其中的 AppID 为你的小程序在微信后台的 appid。

修改以上信息后，可以直接通过 HBuilder（Uniapp IDE）发版了，好好玩吧。

了解更多信息可以阅读[在线文档](https://www.maximtop.com/docs/)，或者在本仓库提问 :)
